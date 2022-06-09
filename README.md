# ETL_pipeline


Let's develop an algorithm to predict which low budget movies being released will become popular so that Amazing Prime can buy the streaming rights at a bargain. We have two data sources
1) a scrape of Wikipedia for all movies released since 1990 stored as a JSON
2) rating data from Movie Land's website from Kaggle stored in CSVs

We shall extract data from these two sources, transform them into one clean dataset, and load this cleaned dataset into a SQL table.

Raw data exists in multiple places and needs to be cleaned and structured before it can be analyzed. ETL breaks this process into three steps
1) Extract: read the data, often from multiple sources (csv, api, json, excel, web scraping, data queries)
2) Transform: clean and structure the data in desired form
3) Load: write the data into a database for storage
 
 
 Transformation can include reading data into pandas dataframe and removing null values, removing entire columns. The data may need to be filtered, parsed, translated, sorted, interpolated, pivoted, summarized, aggregated, merged, or more. Our goal is to create a consistent structure in the data.The transformation phase can be accomplished with python and pandas, pure SQL, specialized ETL tools like Apache Airflow or Microsoft SQL Server Integrated Services(SSIS). Python and pandas are particularly neat because they provide flexibility and interactivity.
 
 We can load our data into its final destination. this can be done with python directly by using the panas to_sql method paired with sqlAlchemy. The data target can be a relational database like PostgreSQL, a non-relational database like MongoDB that stores individual documents, or a data warehouse like Amazon Redshift that optimizes performance specifically for analytics.
 
 ETL can be a one-time migration from one database to another, or as complex as an ongoing automated collection of messy, real-time data from many different sources.
 
 In the extract phase, data is pulled from external or internal sources, possibly disparate.
 
 Let's download our wikipedia json file. We import our dependencies: json, pandas, and numpy.  A variable is then defined for the directory that is holding our data. While it is tempting to read the JSON files directly into a pandas dataframe with the read_json method that is already built into the pandas library, this only works well for data that is already clean(like a json data that has every field filled in- we call this a "flat" file).
 
 Most data we handle will not come in a flat format. However, one plus about JSON is that the format is very flexible and it can handle messy, raw data. But if we try to read raw, messy JSON data into a dataframe, our dataframe will be messy as well and fixing corrupted data in messy dataframes is a headache. Thus for our purposes we will load the raw JSON file as a list of dicitionaries before converting it to a dataframe. 
-when opening files in python, we want to use the "with" statement to handle the file resource

*add code*

Our wiki_movie_raw is now a list of dicts. Let us check how many records were pulled in with the len() function. We have 7,311 movies. We should first consider if this number is reasonable(not too big or too small which could indicate something wrong with our data).

Let's inspect some individual data. If we were working with a dataframe, we would use the head() or tail() method. But with a list of dicts we will be using index slices to select specific chunks of our data.

MovieLens is a website run by a research group at the University of Minnesota. Our kaggle dataset pulls from the MovieLens dataset of over 20 million reviews as well as a metadata file with details about the movies from the Movie Database(TMDb). This data was directly loaded into Mac Finder and because they are already flat-file formats, we can just pull them into Pandas Dataframe directly

## Transformation

For this task, our transformation step will largely consist of data cleanup. We will inspect, plan, and execute.

Bad data comes in 3 forms:
* Beyond repair
* Badly damaged
* Wrong form

The state of our data largely determines which strategy we should use to clean it.

Data beyond repair: data that has been overwritten or has suffered severe data corruption during storage or transfer(power loss during writing, voltage spikes, or hard-drive failures). Worst case scenario the data is missing every value and is thus unrecoverable. In this case we delete and move on.

Data that is badly damaged: may contain good data to recover but will take time and effort to repair damaged data. Can have lots of missing values, inconsistent sources, or existing in multiple columns. We do the best we can with this such as:
* Filling in missing data by
  * substituting data from another source
  * interpolating between existing data points
  * extrapolating from existing data
* Standardizing units of measure( monetary values stored in multiple currencies)
* Consolidating data from multiple columns

Data in the wrong form: can usually be fixed. Our good data is just hidden away in a tricky format. This data can be too granular or detailed, numeric data stored as strings, data that needs to be split into multiple columns, etc. We can try the following:
* Reshape the data
* Convert the data types
* Parse text data to correct format
* Split columns


Our iterative process of data-claning shall be broken down in the following steps:
1) Inspect our data and identify a problem
2) Plan and decide whether its worth the time and effort to fix it
3) We execute the repair

In general, earlier iterations try to handle big chunks of data at one time like removing columns and rows while later iterations focus on smaller chunks of data like parsing values. 

Some good questions to ask before inspecting our data:
* Does it have consistent structure(like a CSV) or is it unstructures?
* How is each data point identified? Will we need to create a unique ID for each data point?

We must first count how many data points or rows exist. If the data is unstructured, count number of columns and missing values in each column. If possible, count number of unique values in each columm amd how frequently they appear. We must investigate the data types to do this.

Once we have identified any problems in our data, we want to answer more questions:
* If a column doesnt have the right data type- does the problem lie with the whole column or just a handful of rows?
* Do rows have outliers due to spurious data or are they valid data points?
* Will our missing values need to be removed, replaced, or interpolated?

Answering these questions will tell us how to modify our data. We will either modify values or modify the structure.

Modifying data values includes removing rows or columns, replacing values, or generating new columns from old ones. We can conver columns to a new data type or bin data(similar to rounding to the nearest hundred). We may split an existing column into several new columns

## We transform our data

When inspecting our wiki_movies data, we notice that we have many, many columns. So much so that the pandas dataframe does not even allow us to view all of them at once. In order to get a sense of how many columns/what kind of data we are working with, we convert the columns into a list.

We can then select the columns that will be useful to us and filter out columns we will not need in our analysis. We will loop through the wiki movies JSON file and keep entries that have a director key, an IMDB link key,and do not have # of seasons key.
 
 
 
 
 
 
 
 
 

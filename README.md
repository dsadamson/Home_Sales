# Using SparkSQL to Track Home Sales Metrics

# Overview 

For this project, I was provided with a link to a .csv file containing home sales data over the past several years. I was then asked to use SparkSQL to track this data. This assignment aimed to demonstrate proficiency in the following: use Spark to create temporary views, partition the data, cache and uncache a temporary table, and verify that the table has been uncached.

# Instructions

This project was coded in Google Colab. Be sure to configure Spark, so it will operate with Google Colab. Or, if you are running this in a coding environment on your local machine, adjust your configuration of Spark to reflect the location of your imports, in the first cell.

# Features

The Jupyter Notebook begins by importing all Spark and Java dependencies needed for this assignment, configuring to the Google Colab environment. 
Next, it reads and displays the DataFrame of a .csv, taken from Amazon Web Services. 
The 'date_built' column is changed to a date-type data point for later SQL queries, and a temporary view of the DataFrame is created.

Then, the Jupyter Notebook features four SQL queries, each of which follow a similar syntax:

    #5. What is the average price of a home for each year built that have 3 bedrooms, 3 bathrooms, with two floors,
    
    #and are greater than or equal to 2,000 square feet rounded to two decimal places?
    
    query = """
    
    SELECT YEAR (date_built) AS year_built, ROUND(AVG(price),2) AS average_price
    
    FROM home_sales
    
    WHERE bedrooms = 3 AND bathrooms = 3 AND floors = 2 AND sqft_living >= 2000
    
    GROUP BY year_built
    
    ORDER BY year_built
    
    """
    
    query_df = spark.sql(query)
    
    query_df.show()


The fourth query also features a subquery and a temporary view of that subquery's results, as home prices first need to be parsed and sorted. Furthermore, the time elapsed during the code's run time is tracked and displayed. A snippet of the code is featured below:

    #6. What is the "view" rating for the average price of a home, rounded to two decimal places, where the homes are greater than
    #or equal to $350,000? Although this is a small dataset, determine the run time for this query.
    
    # Begin tracking run time of query
    start_time = time.time()
    
    # Establish subquery to calculate the average price
    subquery = """
    SELECT view, ROUND(AVG(price),2) AS average_price
    FROM home_sales
    GROUP BY view
    ORDER BY view
    """
    
    # Create a temporary view for the subquery
    spark.sql(subquery).createOrReplaceTempView('average_prices')
    
    # Main query to filter the results based on average_price
    query = """
    SELECT view, average_price
    FROM average_prices
    WHERE average_price < 350000
    """
    
    # Display query results
    query_df = spark.sql(query)
    query_df.show()
    
    # Print runtime for the query
    print("RUN TIME: %.2f seconds" % (time.time() - start_time))

The temporary view of the full data set is cached, and the same subquery and query are run again, with their run time being tracked again. 

Finally, the dataset is exported as a parquet file, and it is read into the code. The same subquery and query are run and timed again. 

In the final cells, the temporary view of the full dataset is uncached.

# Source

Data for this dataset was generated by edX Boot Camps LLC, and is intended for educational purposes only.

# Author

Daniel Adamson

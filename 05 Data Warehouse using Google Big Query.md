# Data Warehouse using Google BigQuery

## Overview of Google BigQuery

## Create Dataset and Tables in Google BigQuery
Let us go ahead and see how we can create Dataset and an empty table using Google BigQuery.
* Once we login into BigQuery UI, we can create data set under the project. Let us create data set by name **retail**.
* Make sure to choose **Empty table** under **Create table from**.
* Make sure to choose appropriate project id and data set (**retail**).
* Specify the table name as `orders`.
* Enter one column at a time using wizard. You can use the below details as reference.
```
order_id:INTEGER,
order_date:TIMESTAMP,
order_customer_id: INTEGER,
order_status:STRING
```

Exercise: Create Empty tables for order_items and products.

## Setup Service Account for Google BigQuery
Here are the instructions to setup service account for Google BigQuery.
* You can use instructions in this [document](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-client-libraries) to get instructions related to Google Client Service Accounts.
* Make sure to choose appropriate Project.
* Enable BigQuery API 
* Create Service Account with Owner Role
* Add and Download the Keys

## Load Data into an Empty Table in Google BigQuery
Here is the Python based approach to load data from files in GCS into Google BigQuery Table.
* We need to make sure we instantiate the client.
* Create Job Config using Schema of the table that needs to be populated.
* Use `client.load_table_from_uri` to populate the table from the file under URI using the job config.

You can go through the notebook and make required changes to see if it works as expected or not. You can also validate by running queries against the table using Google BigQuery UI.

Exercise: Populate `order_items` as well as `products` tables.

## Create Tables using GCS Files in Google BigQuery
Let us go ahead and create table for `orders` using Web UI of Google BigQuery. The table will be created using GCS Files.
* After choosing Cloud Storage as source, we can specify column names like this as our data doesn't have header. We need to make sure the data type for `order_date` is defined as `TIMESTAMP`.
```
order_id:INTEGER,
order_date:TIMESTAMP,
order_customer_id: INTEGER,
order_status:STRING
```

## SQL Capabilities of Google BigQuery
Here are some of the SQL Capabilities of Google BigQuery.
* All Standard SQL Features.
  * Robust set of pre-defined functions
  * Filtering of the data
  * Joins (Inner as well as Outer)
  * Standard Aggregations
  * Cumulative Aggregations
  * Ranking as well as Window Functions
* Ability to process JSON Data. 

## Compute Daily Product Revenue using Google BigQuery
Here is the query to compute daily product revenue using Google BigQuery. The query covers most of the basic abilities of SQL using Google BigQuery such as Filtering, Joins, Aggregations, etc.

```sql
SELECT o.order_date,
    oi.order_item_product_id,
    round(sum(oi.order_item_subtotal), 2) AS revenue
FROM retail.orders AS o
    JOIN retail.order_items AS oi
      ON o.order_id = oi.order_item_order_id
WHERE o.order_status IN ('COMPLETE', 'CLOSED')
GROUP BY 1, 2
ORDER BY 1, 3 DESC
```

## Cumulative Aggregations and Ranking using Google BigQuery
Google BigQuery also supports cumulative aggreations and ranking.
* Cumulative Aggregations using daily revenue

```sql
WITH daily_revenue AS (
    SELECT o.order_date,
        round(sum(oi.order_item_subtotal), 2) AS revenue
    FROM retail.orders AS o
        JOIN retail.order_items AS oi
        ON o.order_id = oi.order_item_order_id
    WHERE o.order_status IN ('COMPLETE', 'CLOSED')
    GROUP BY 1
) SELECT format_date('%Y%m', order_date) AS order_month,
    order_date,
    revenue,
    round(sum(revenue) OVER (
        PARTITION BY format_date('%Y%m', order_date)
        ORDER BY order_date
    ), 2) AS revenue_cum
FROM daily_revenue
ORDER BY 2;
```

* Top 3 Monthly Products by revenue

```sql
WITH daily_product_revenue AS (
    SELECT o.order_date,
        oi.order_item_product_id,
        round(sum(oi.order_item_subtotal), 2) AS revenue
    FROM retail.orders AS o
        JOIN retail.order_items AS oi
        ON o.order_id = oi.order_item_order_id
    WHERE o.order_status IN ('COMPLETE', 'CLOSED')
    GROUP BY 1, 2
) SELECT * FROM (
    SELECT format_date('%Y%m', order_date) AS order_month,
        order_date,
        order_item_product_id,
        revenue,
        dense_rank() OVER (
            PARTITION BY order_date
            ORDER BY revenue DESC
        ) AS drank
    FROM daily_product_revenue
) WHERE drank <= 3  
ORDER BY 2, 4 DESC
```

## Overview of Key Integrations with Google BigQuery
Let us get an overview of Key Integrations with Google BigQuery.
* Integration with GCS
* Integration with External Databases such as Cloud SQL
* Spark Connector on Dataproc (Covered as part of subsequent modules)
* Programming Languages such as Python

## Python Pandas Integration with Google BigQuery
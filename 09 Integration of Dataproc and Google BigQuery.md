# Integration of Dataproc and BigQuery

## Setup Development Environment using Dataproc
Make sure to use VS Code Remote Development Extension Pack and Setup Development Environment using Dataproc.
* We have already gone through the details in previous section.

## Validate Google BigQuery Integration with Python
Let us validate Google BigQuery Integration with Python.
```python
from google.cloud import bigquery

client = bigquery.Client()
QUERY = (
    'SELECT * FROM `itversity-rnd.retail.orders` '
    'LIMIT 10')
query_job = client.query(QUERY)
rows = query_job.result()

for row in rows:
    print(row.order_id)
```

## Validate Google BigQuery Integration with Pyspark
Let us go ahead and validate Google BigQuery Integration with Pyspark. We need to make sure Pyspark is launched with appropriate jars of Google BigQuery Spark Connector.
* Here is the command used based on the version of Scala using which Spark is developed.
```shell
pyspark --jars gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.26.0.jar
```
* Here is sample Spark Code to read from BigQuery Table.
```python
df = spark. \
    read. \
    format('bigquery'). \
    option('table', 'itversity-rnd:retail.orders'). \
    load()

df.printSchema()

df.orderBy('order_id').show()
```

## Review Logic to Compute Daily Product Revenue
Let us review the logic to compute daily product revenue. We have already deployed workflow using Spark SQL Commands.
* Jobs to take care of file format converter for `orders` and `order_items`.
* Job to compute daily product revenue and save the results to a folder in GCS.
* We now need to create Pyspark Job to save the daily product revenue to BigQuery Table.

## Create Table in Google BigQuery
Let us go ahead and create Table in Google BigQuery to preserve daily product revenue.
* We can use the `CREATE TABLE` command that is part of the script used to compute daily compute revenue as reference.
```sql
CREATE TABLE IF NOT EXISTS retail.daily_product_revenue (
    order_date STRING,
    order_item_product_id INTEGER,
    order_status STRING,
    product_quantity INTEGER,
    product_revenue DECIMAL
);
```

## Develop Logic to Save Result to BigQuery Table
Now it is time for us to develop the logic to write data in Dataframe to BigQuery Table.
* Create Dataframe using data in GCS files.
* Set GCS bucket as temporary bucket.
* Develop logic to write to Google BigQuery Table.

```python
daily_product_revenue = spark. \
    read. \
    parquet('gs://airetail/retail_gold.db/daily_product_revenue')

spark.conf.set('temporaryGcsBucket', 'airetail')

daily_product_revenue. \
    write. \
    mode('overwrite'). \
    format('bigquery'). \
    option('table', 'itversity-rnd:retail.daily_product_revenue'). \
    save()
```

## Validate by Querying Google BigQuery Table

## Reset Table in Google BigQuery
Let us go ahead and truncate the table in BigQuery, so thate we can deploy the Spark Job to load the data into the table.
* We can use `TRUNCATE TABLE retail.daily_product_revenue` to truncate the table.

## Build Dataproc Spark Job with BigQuery Integration

Here are the instructions to submit the job in client mode.
* Export all the relevant variables. Make sure to update values based on your environment.
```shell
export DATA_URI='gs://airetail/retail_gold.db/daily_product_revenue'
export PROJECT_ID='itversity-rnd'
export DATASET_NAME='retail'
export GCS_TEMP_BUCKET='airetail'
```
* Run `spark-submit` to submit the job.
```shell
spark-submit \
    --jars gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.26.0.jar \
    app.py
```

Here are the instructions to submit Spark Job using cluster mode.
```shell
spark-submit \
    --master yarn \
    --deploy-mode cluster \
    --jars gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.26.0.jar \
	--conf "spark.yarn.appMasterEnv.DATA_URI=gs://airetail/retail_gold.db/daily_product_revenue" \
	--conf "spark.yarn.appMasterEnv.PROJECT_ID=itversity-rnd" \
	--conf "spark.yarn.appMasterEnv.DATASET_NAME=retail" \
	--conf "spark.yarn.appMasterEnv.GCS_TEMP_BUCKET=airetail" \
	app.py
```
## Copy the Application to GCS
Make sure to copy the application to GCS so that we can create the job without any issue using Dataproc.
* One can validate whether the application can be run from GCS or not by submitting the job using `spark-submit`.

## Deploy and Run Dataproc Spark Job
Here are the configurations related to Dataproc Spark Job.
* Job type: **PySpark**
* Main python file: **gs://airetail/apps/daily_product_revenue/app.py**
* Jar files: **gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.26.0.jar**
* Properties

|Key|Value|
|---|---|
|spark.yarn.appMasterEnv.DATA_URI|gs://airetail/retail_gold.db/daily_product_revenue|
|spark.yarn.appMasterEnv.PROJECT_ID|itversity-rnd|
|spark.yarn.appMasterEnv.DATASET_NAME|retail|
|spark.yarn.appMasterEnv.GCS_TEMP_BUCKET|airetail|
|spark.submit.deployMode|cluster|

## Add Spark Job to the Dataproc Workflow

## Run and Validate the Dataproc Workflow

## Demo of End to End Data Pipeline Workflow
### Add static IP address

1) Create or turn on dataproc cluster
2) Enter to dataproc cluster instance
3) Go to VM Instance tab
4) Choose master node instance
5) In Network interfaces click on View details of master node instance
6) On the menu at the left, click on Ip Addresses
7) Attach Static IP Address clicking on Change button and select the master instance. This will assign the static ip address to the master vm instance of the cluster

### Connect to gcloud dataproc

#### 1. Configure gcloud connection

1) run `gcloud compute config-ssh`
2) execute command suggested by gcloud to connect, example: `ssh cluster-3fb9-m.us-central1-f.dataanalytics-347914`

#### 2. Use ssh command from vm instance. This will create a new ssh keys to connect

Two options:

1. gcloud compute ssh --zone "us-central1-f" "cluster-3fb9-m" --project "dataanalytics-347914"
2. connect using ssh command `ssh -i ~/.ssh/google_compute_engine mlevydaniel@34.122.45.243`

Important note: In case it appears a warning message remove the existing RSA key from the known_hosts file in ssh folder: https://serverfault.com/questions/321167/add-correct-host-key-in-known-hosts-multiple-ssh-host-keys-per-hostname

Option 1 is preferred because it automatically configures everything to later connect using VSCode. When using the second option, you will have to configure the ssh config file manually

### Connect using VScode

1) Download "Remote - SSH extension" developed by Microsoft
2) ssh config file needs to include the cluster instance information. Using `gcloud compute config-ssh` command will do all the configuration automatically

ssh's config file stores different ssh configurations to access each key easily. For example, using only `ssh hostname` will connect without any other parameter

#### Clone project

1) connect to host and clone repository: https://github.com/Mlevydaniel/data-engineering-on-gcp.git

#### Transfer files to HDFS

1. From VM file system to HDFS

1) Create public directory in HDFS: `hdfs dfs -mkdir /public` and check that it was created successfully: `hdfs dfs -ls /`
2) Copy using `hdfs dfs -put data/retail_db /public` and it will copy recursively
3) Check using `hdfs dfs -ls -R /public/retail_db`

In case of mistakes, delete using command: `hdfs dfs -rm -R /public/retail_db`

2. From GCS to HDFS
1) run the command `hdfs dfs -cp gs://airetail_mld/retail_db /public`

#### Start CLIs

1) pyspark
2) spark-shell
3) spark-sql

#### Copy to gcs

Run command: `gsutil cp -r data/retail_db_json gs://airetail_mld/retail_db_json`

#### Validate data

We can review each table in GCS from dataproc cluster directly without importing it first into the HDFS!

1) spark-sql
2) SELECT * FROM JSON.`gs://airetail_mld/retail_db_json/orders` LIMIT 10;

#### Run spark-sql scripts

1) Execute commands:
`spark-sql -f ./scripts/daily_product_revenue/file_format_converter.sql -d bucket_name=gs://airetail_mld -d table_name=orders`
and
`spark-sql -f ./scripts/daily_product_revenue/file_format_converter.sql -d bucket_name=gs://airetail_mld -d table_name=order_items`

-f: execute script from file
-d: argument value in key=value format

spark-sql lets us create databases in GCS and use them as an ordinary database storing all the tables in parquet format. To get the databases and tables, use:

`SHOW databases;`
`USE database; SHOW tables;`
`SELECT * FROM database.table LIMIT 100;`

2) Execute final script: `spark-sql -f ./scripts/daily_product_revenue/compute_daily_product_revenue.sql -d bucket_name=gs://airetail_mld`

### Executing from GCS
#### Move scripts

Move to GCS
`gsutil cp -r scripts/daily_product_revenue gs://airetail_mld/scripts/daily_product_revenue`

#### Execute
`spark-sql -f gs://airetail_mld/scripts/daily_product_revenue/cleanup.sql`
`spark-sql -f gs://airetail_mld/scripts/daily_product_revenue/file_format_converter.sql -d bucket_name=gs://airetail_mld -d table_name=orders`
`spark-sql -f gs://airetail_mld/scripts/daily_product_revenue/file_format_converter.sql -d bucket_name=gs://airetail_mld -d table_name=order_items`
`spark-sql -f gs://airetail_mld/scripts/daily_product_revenue/compute_daily_product_revenue.sql -d bucket_name=gs://airetail_mld`


### gcloud CLI

#### Set up gcloud CLI dataproc region

`gcloud config set dataproc/region us-central1`

#### Help section

Manual:
`gcloud help dataproc clusters`

clusters commands:
`gcloud dataproc clusters`

#### commands
`gcloud dataproc clusters list`
`gcloud dataproc clusters describe airetail_mld`

#### Submit jobs

Run scripts:
1. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -e "SELECT CURRENT_DATE()"`
2. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -f gs://airetail_mld/scripts/daily_product_revenue/cleanup.sql`
3. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -f gs://airetail_mld/scripts/daily_product_revenue/file_format_converter.sql --params=bucket_name=gs://airetail_mld,table_name=orders`
4. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -f gs://airetail_mld/scripts/daily_product_revenue/file_format_converter.sql --params=bucket_name=gs://airetail_mld,table_name=order_items`
5. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -f gs://airetail_mld/scripts/daily_product_revenue/compute_daily_product_revenue.sql --params=bucket_name=gs://airetail_mld`

Validate:
1. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -e "USE retail_bronze_db;SHOW tables;"`
2. `gcloud dataproc jobs submit spark-sql --cluster=cluster-256f -e "USE retail_gold_db;SHOW tables;"`

### Dataproc Workflows

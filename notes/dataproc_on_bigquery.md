### Connect using VScode

1) Download "Remote - SSH extension" developed by Microsoft
2) ssh config file needs to include the cluster instance information. Using `gcloud compute config-ssh` command will do all the configuration automatically

ssh's config file stores different ssh configurations to access each key easily. For example, using only `ssh hostname` will connect without any other parameter

### Start pyspark with jars connector

The jar file contains the required configuration needed to connect to BigQuery DBMS

1) Connecto to Dataproc vm
2) Execute the following command: `pyspark --jars gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.31.0.jar`


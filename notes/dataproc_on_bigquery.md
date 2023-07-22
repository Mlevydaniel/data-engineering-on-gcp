### Connect using VScode

1) Download "Remote - SSH extension" developed by Microsoft
2) ssh config file needs to include the cluster instance information. Using `gcloud compute config-ssh` command will do all the configuration automatically

ssh's config file stores different ssh configurations to access each key easily. For example, using only `ssh hostname` will connect without any other parameter

### Start pyspark with jars connector

The jar file contains the required configuration needed to connect to BigQuery DBMS from a Dataproc VM

1) Connect to Dataproc vm using ssh
2) Execute the following command: `pyspark --jars gs://spark-lib/bigquery/spark-bigquery-with-dependencies_2.12-0.31.0.jar`

The jar files are by default stored in GCS in the following location: `gs://spark-lib/bigquery/`. Choose the version that matches the version of Spark installed on the Dataproc VM (run pyspark to initialize it and it will display the version)

## Cloud Composer

### Install relevant packages

### Executed airflow commands

Airflow commands are divided into three components: First part + command + subcommand

`gcloud composer environments run --location=us-central1 --project=dataanalytics-347914 airflow` + `dags` + `list`

#### Check version Locally
`airflow version`

#### Check version Remote
`gcloud composer environments run --location=us-central1 --project=dataanalytics-347914 airflow version`

#### Other aiflow commands: dags
`gcloud composer environments run --location=us-central1 --project=dataanalytics-347914 airflow dags list`

### Deploy a dag

To deploy a dag, copy the script into the dags folder in GCS:
`gsutil cp apps/airflow_dags/tutorial_dag.py gs://bucket/dags/`

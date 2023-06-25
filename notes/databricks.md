### Create new Databricks CLI profile

1. Create virtual environment:
`python3.9 -m venv dd-venv && pip install databricks-cli`
2. Generate new token in User Settings
3. run command `databricks configure --host https://6927445907976758.8.gcp.databricks.com --token --profile analyticsgcp`
4. It will request for the token, paste it and push enter

To validate run the command:
`databricks fs ls --profile analyticsgcp`

### Copy files locally to DBFS

1. Run command `databricks fs cp data/retail_db dbfs:/public/retail_db --recursive --profile analyticsgcp`
2. Validate: `databricks fs ls dbfs:/public/retail_db --profile analyticsgcp`

Important: always pass the profile unless it is the default user (use --profile parameter)
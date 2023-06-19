### Create new Databricks CLI profile

1. Generate new token in User Settings
2. run command `databricks configure --host https://2631378408200327.7.gcp.databricks.com --token --profile anal
yticsgcp`
3. It will request for the token, paste it and push enter

To validate run the command:
`databricks fs ls --profile analyticsgcp`

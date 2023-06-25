-- GCP GKE USEFUL COMMANDS --

### Manage containers
gcloud container clusters
gcloud container clusters resize db-5409595159028339-9-0624-222058-376 --node-pool=system-pool-20230624-222059 --num-nodes=0 --region=us-central1
gcloud container clusters delete db-5409595159028339-9-0624-222058-376 --region=us-central1


## Manage Node Pools
gcloud container node-pools
gcloud container node-pools delete --help
gcloud container node-pools delete --cluster=db-5409595159028339-9-0624-222058-376 --region=us-central1
gcloud container node-pools list --region=us-central1 --cluster=db-5409595159028339-9-0624-222058-376

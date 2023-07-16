### Transfer ssh files from local to remote server to access github

#### use ssh specific keys to connect to remote server
`ssh -v -i ~/.ssh/google_compute_engine mlevydaniel@34.122.45.243`

#### Copy files from local to remote using the same keys as before
`scp -i ~/.ssh/google_compute_engine id_rsa* mlevydaniel@34.122.45.243:/home/mlevydaniel/.ssh`

#### Change remote url from https to ssh
`git remote set-url origin git@github.com:Mlevydaniel/data-engineering-on-gcp.git`





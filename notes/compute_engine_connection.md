## Connect to GCP Compute Engine Server

1. Create instance
2. Connect via SSH with the following command: `gcloud compute config-ssh`
3. After configuring ssh, execute command suggested by gcloud to connect, example: `ssh cluster-b442-m.us-central1-f.dataanalytics-347914`
4. Start jupyter notebook server: `jupyter-notebook --no-browser --port=<PORT-NUMBER --notebook-dir=.>`
5. Open browser and go to: `http://127.0.0.0:<PORT-NUMBER>`

Important: Once connected via ssh, we can open the notebook in our browser through localhost or 127.0.0.0. It isn't possible to connect using the external ip through the browser directly, it is necessary to first create the ssh connection and then open it as localhost
### Airflow with Astro

Astro CLI let's us deploy and manage Airflow servers easily. To install it just execute:

`brew install astro`

#### Create a new Astro project in your working directory

This function creates all the files, folders and settings to run right away Airflow

`astro dev init`

Some other packages are important for local development. It is recommended to create a new virtual environment and install
the same version of Python and airflow packages. The steps are the following:

1. Check Python version in Airflow scheduler: `astro dev bash scheduler` and then `python --version`
2. Create a virtual environment with the same python version: `python -m venv -p /usr/bin/python3.8 my_python_env`
3. Activate the virtual environment
4. Check that the virtual environment has the same version of Python: `python --version`
5. Check Airflow version from the Web UI
6. Install the same version of Airflow in the virtual environment: `pip install apache-airflow==2.7.0`

#### Start a local Airflow environment

`astro dev start`

#### Check that local Airflow environment is running

`astro dev ps`

#### Kill all locally running Airflow containers

When killing and then starting a new airflow environment, the same name is used

`astro dev kill`

#### Stop all locally running Airflow containers

`astro dev stop`

#### Connect to one container

`astro dev bash scheduler`

#### Check logs

`astro dev logs -s`

Check logs continuously (it refreshes): `astro dev logs -s -f`

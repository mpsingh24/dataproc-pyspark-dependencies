# Dataproc PySpark Artifact Repository Integration

## Purpose
This document provides a structured approach to setting up a Google Cloud Dataproc cluster with a PySpark environment that integrates with an Artifact Repository. It includes an initialization action script for environment setup, Dataproc cluster creation, and a job submission example.

---

## Initialization Action Script
This script sets up a Python virtual environment and installs necessary dependencies.

```sh
# Create a Python virtual environment
python -m venv /usr/local/venv

# Activate the virtual environment
source /usr/local/venv/bin/activate

# Install necessary packages
pip install keyring
pip install keyrings.google-artifactregistry-auth
pip install --no-deps --index-url https://northamerica-northeast1-python.pkg.dev/demoprj/pypi2/simple/ --verbose generate-dummy-data
```

---

## Creating a Dataproc Cluster
This command creates a Dataproc cluster with the specified configurations and applies the initialization script.

```sh
gcloud dataproc clusters create cluster-jobs02 \
  --region northamerica-northeast1 \
  --image-version 2.2-debian12 \
  --scopes 'https://www.googleapis.com/auth/cloud-platform' \
  --project demoprj \
  --public-ip-address \
  --initialization-actions gs://demoprjbucket/init_action.sh
```

---

## Submitting a PySpark Job
This command submits a PySpark job to the Dataproc cluster, specifying the Python environment.

```sh
gcloud dataproc jobs submit pyspark gs://demoprjbucket/scripts/entrypoint.py \
  --cluster=cluster-jobs101 \
  --region=northamerica-northeast1 \
  --properties="spark.pyspark.python=/usr/local/venv/bin/python,spark.pyspark.driver.python=/usr/local/venv/bin/python"
```

---

## Notes
- Ensure that the initialization script is stored in a Cloud Storage bucket accessible by Dataproc.
- The cluster's service account must have the required permissions to access the Artifact Registry and other resources.
- Adjust parameters such as project ID, region, and script locations as needed.

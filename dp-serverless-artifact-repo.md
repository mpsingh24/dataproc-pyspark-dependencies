# Dataproc Serverless PySpark Custom Container Image

## Purpose
This document provides a well-structured guide for integrating a Dataproc Serverless PySpark job with an Artifact Repository using a custom container image. The custom container image includes necessary dependencies and configurations for smooth execution.

## Prerequisites
- Google Cloud SDK installed and authenticated.
- Access to a Google Cloud Artifact Registry.
- A Dataproc Serverless environment.

## Custom Container Image

### Base Image Selection
The recommended base image for the custom container is **Debian 12 (slim version)**.

```dockerfile
# Use Debian 12 slim as the base image
FROM debian:12-slim

# Suppress interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install system utilities required for Spark scripts
RUN apt update && apt install -y procps tini libjemalloc2

# Enable jemalloc2 as the default memory allocator
ENV LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libjemalloc.so.2

# Install additional utilities for XGBoost support
RUN apt install -y procps libgomp1

# Define Miniconda installation path
ENV CONDA_HOME=/opt/miniforge3
ENV PYSPARK_PYTHON=${CONDA_HOME}/bin/python
ENV PATH=${CONDA_HOME}/bin:${PATH}

# Download and install Miniconda
ADD https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh .
RUN bash Miniforge3-Linux-x86_64.sh -b -p /opt/miniforge3 \
  && ${CONDA_HOME}/bin/conda config --system --set always_yes True \
  && ${CONDA_HOME}/bin/conda config --system --set auto_update_conda False \
  && ${CONDA_HOME}/bin/conda config --system --set channel_priority strict

# Install pip packages
RUN ${PYSPARK_PYTHON} -m pip install \
    faker \
    keyring \
    keyrings.google-artifactregistry-auth


# Install custom packages from Google Artifact Registry
RUN ${PYSPARK_PYTHON} -m pip install --no-deps \
    --index-url https://northamerica-northeast1-python.pkg.dev/demoprj/pypi2/simple/ \
    --verbose generate-dummy-data

# Create the 'spark' group/user with UID and GID 1099
RUN groupadd -g 1099 spark
RUN useradd -u 1099 -g 1099 -d /home/spark -m spark
USER spark
```

## Build and Push the Container Image
We build and push the container image to Google Artifact Registry.

```sh
# Build and push the container image
gcloud builds submit --region=northamerica-northeast1 \
--tag northamerica-northeast1-docker.pkg.dev/demoprj/custom-images/pyspark-image:0.1.0
```

## Job Submission Using the Custom Container
After the image is built and pushed, we submit a Dataproc Serverless PySpark job using the custom container.

```sh
# Submit a Dataproc Serverless PySpark job
gcloud dataproc batches submit pyspark gs://demoprjbucket/scripts/entrypoint.py \
--container-image=northamerica-northeast1-docker.pkg.dev/demoprj/custom-images/pyspark-image:0.1.0 \
--region=northamerica-northeast1
```

## Summary
This guide provides a streamlined approach to integrating a Dataproc Serverless PySpark job with an Artifact Repository using a custom container image. The image is configured with required dependencies, custom package installation, and optimized settings for Spark execution.

# README

## Purpose
This repository provides structured guides for integrating Google Cloud Dataproc with an Artifact Repository in PySpark environments. It covers both **Dataproc Serverless** and **Dataproc Cluster-based** approaches, ensuring seamless dependency management and execution of PySpark jobs.

## Documents Overview

### [Dataproc Serverless PySpark Custom Container Image](dp-serverless-artifact-repo.md)
This guide explains how to build and configure a custom container image for Dataproc Serverless. It details:
- Installation of required system dependencies.
- Configuration of Miniconda for Python package management.
- Authentication and installation of custom packages from an Artifact Repository.
- Image building and job submission using `gcloud` commands.

### [Dataproc GCE PySpark Artifact Repository Integration](dpgce-pyspark-artifact-repo.md)
This document provides a structured approach to setting up a **Dataproc cluster-based** PySpark environment with Artifact Repository integration. It includes:
- An initialization action script for configuring the cluster.
- Steps for Dataproc cluster creation with necessary configurations.
- Example job submission to test the integration.

## Usage
Refer to the respective documents based on your requirements:
- Use **Dataproc Serverless** for a **fully managed** and containerized execution environment.
- Use **Dataproc Cluster-based** for more control over cluster configurations and long-running jobs.

Follow the steps outlined in each guide to set up your environment efficiently.

Container on GKE
================

Scripts to help bootstrapping VMs on [Google Container
Engine (GKE)](http://cloud.google.com/container-engine/) to run a self-updating
[Go](https://golang.org) webserver:



Project setup
-------------

### 1. Install and setup `gcloud`

Install `gcloud` and restart your shell:

    curl https://sdk.cloud.google.com | bash

Make sure you [created a project and enabled billing (with free
trial)](https://console.developers.google.com/) then initialize `gcloud`:

    gcloud init --console-only
    gcloud config set compute/zone us-central1-f
    gcloud auth list
    gcloud config list


### 2. Enable APIs

You'll want to enable a few APIs. Enabling the API is itself free:

    - Google Cloud Monitoring
    - Google Cloud Logging


### 3. Install Docker

Visit https://docs.docker.com/installation/ for OS-specific instructions. You
may want to repeat every few month or so to update.


### 4. Install Kubernetes

https://cloud.google.com/container-engine/docs/clusters/operations#passing_cluster_credentials_to_kubectl

    gcloud components update kubectl

    # This is only needed if you created the cluster via the Web UI:
    gcloud container clusters get-credentials

    ## Install a full local cluster:
    #curl -sS https://get.k8s.io | bash
    #
    ## Client only:
    #go get github.com/kubernetes/kubernetes/cmd/kubectl


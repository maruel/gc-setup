VM
==

Scripts to help bootstrapping VMs on [Google Compute
Engine (GCE)](http://cloud.google.com/compute) to run a self-updating
[Go](https://golang.org) webserver:

  - [make_startup](make_startup) generates customized startup
    [script](https://cloud.google.com/compute/docs/startupscript). This
    simplifies quickly deploying from vanilla stock image:
    - Golang [facebookgo/grace](https://github.com/facebookgo/grace)
      based service transparent zero-downtime service restart via [Github
      webhooks](https://developer.github.com/webhooks/) using
      [phayes/hookserve](https://github.com/phayes/hookserve).
    - Google [Domains Dynamic
      DNS](https://support.google.com/domains/answer/6147083); hostname updated
      on boot.
    - Google [Cloud Logging](https://cloud.google.com/logging/); streams logs to
      your Cloud console.
    - Really simple to use, doesn't require a CI, doesn't use a docker image.
      Just `git push` and your server is immediately rebuilt and restarted.
  - [create](create.md) easily create a VM with the right ports opened and
    using the startup script.


Usage
-----

First, create a startup.sh script, then create the VM to use it. Here's an
example how to start a [godoc.org](https://godoc.org) clone:

    ./make_startup --help

    ./make_startup \
      --gd-user <gdomains user> --gd-pwd <gdomains pwd> --gd-host godoc.example.com \
      --go-get-url github.com/golang/gddo/gddo-server \
      --pkg redis-server,mercurial

    ./create godoc

    # Wait a few seconds then browse http://godoc.example.com or:
    ssh godoc.example.com


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


### 2. Open ports in the firewall

Setup the [firewall](https://cloud.google.com/compute/docs/networking) as by
default almost all ports are closed.

    gcloud compute firewall-rules create allow-http --description "Incoming http allowed" --allow tcp:80
    gcloud compute firewall-rules create allow-https --description "Incoming https allowed" --allow tcp:443
    gcloud compute firewall-rules create allow-tcp9000 --description "Incoming tcp:9000 allowed" --allow tcp:9000

    gcloud compute firewall-rules list
    gcloud compute networks list


### 3. Push your SSH key

_(Optional, only if you have a ssh key)_

Setup [your ssh key to your Google Cloud
project](https://cloud.google.com/compute/docs/networking), this way you can ssh
natively to your instances from any workstation with your ssh key configured.
[Doc at Canonical](https://wiki.ubuntu.com/GoogleComputeEngineSSHKeys).

    echo -n "$USER " > key.pub
    cat ~/.ssh/id_rsa.pub >> key.pub
    gcloud compute project-info add-metadata --metadata-from-file sshKeys=key.pub
    rm key.pub

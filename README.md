gce-setup
=========

Scripts to help bootstrapping VMs on Google Compute Engine (GCE) to run a Go
webserver:

  - [make_startup](make_startup) generates customized startup
    [script](https://cloud.google.com/compute/docs/startupscript). This
    simplifies quickly deploying from vanilla stock image:
    - Go
      [facebookgo/grace/gracehttp](https://github.com/facebookgo/grace/gracehttp)
      based service transparent zero-downtime service restart via [Github
      webhooks](https://developer.github.com/webhooks/) using
      [hookserve](https://github.com/phayes/hookserve).
    - Google Domains DynDNS; hostname updated on boot.
    - Google Cloud Logging; streams logs to your Cloud console.
    - Really simple to use, doesn't require a CI; it doesn't use a docker image.
  - [create_vm](create_vm.md) easily create a VM with the right ports opened and
    using the startup script.


Usage
-----

First, create a startup.sh script, then create the VM to use it. Here's an
example how to start a godoc.org clone:

    ./make_startup --help

    ./make_startup \
      --gd-user <gdomains user> --gd-pwd <gdomains pwd> --gd-host godoc.example.com \
      --go-get-url github.com/golang/gddo/gddo-server \
      --pkg redis-server,mercurial

    ./create_vm godoc

    # Wait a few seconds then browse http://godoc.example.com or:
    ssh godoc.example.com


Project setup
-------------

Install `gcloud` and restart your shell:

    curl https://sdk.cloud.google.com | bash

Make sure you created a project and enabled billing (with free trial) at
https://console.developers.google.com then initialize `gcloud`:

    gcloud init --console-only
    gcloud config set compute/zone us-central1-f
    gcloud auth list
    gcloud config list


Setup your ssh key, this way you can ssh natively to your instances from
any workstation with your ssh key configured. More details at
https://cloud.google.com/compute/docs/instances/managing-ssh-keys and
https://wiki.ubuntu.com/GoogleComputeEngineSSHKeys

    echo -n "$USER " > key.pub
    cat ~/.ssh/id_rsa.pub >> key.pub
    gcloud compute project-info add-metadata --metadata-from-file sshKeys=key.pub
    rm key.pub

Setup the firewall; as by default almost all ports are closed. https://cloud.google.com/compute/docs/networking

    gcloud compute firewall-rules create allow-http --description "Incoming http allowed" --allow tcp:80
    gcloud compute firewall-rules create allow-https --description "Incoming https allowed" --allow tcp:443
    gcloud compute firewall-rules create allow-tcp9000 --description "Incoming tcp:9000 allowed" --allow tcp:9000
    gcloud compute firewall-rules list
    gcloud compute networks list

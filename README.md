gc-setup
========

Scripts to help bootstrapping either VMs or Containers on [Google
Cloud](http://cloud.google.com) to run a [Go](https://golang.org) webserver with
zero downtime.

  - Turn left, use the [VM route](vm/) with Google Compute Engine (GCE).
    - Perfect for low-cost toy project.
    - Works with f1-micro instance.
    - Doesn't require a load balancer.
  - Turn right, use the [Container route](container/) with Google Container
    Engine (GKE).
    - Perfect for complex app with multiple services (DB, frontend, backend)
      requiring more than a VM anyway.
    - Generate background load of 30% on a g1-small instance so necessitate more
      horse power.
    - Small cost overhead at [network
      balancer](https://cloud.google.com/compute/docs/load-balancing/) (0.8¢/GB + 2.5¢/h).
    - Scales easily.

Create a VM
===========

First you need to have a VM up and running. Here's a boilerplate explanation how
to get a VM on Google Compute Engine up and running and well configured.

First, run `./make_script` to generate a `startup.sh`. Use `./make_script
--help` for more details.

Here's roughly what `create` does for you:

  1. Create the project via the [Google Developers
     Console](https://console.developers.google.com); while I use [Google
     Compute Engine](https://cloud.google.com/compute/) here, [Amazon
     EC2](https://aws.amazon.com/ec2/) works at well. If you use Azure, I don't
     understand you.
  2. Create the VM by selecting _Compute_ / _Compute Engine_ / _VM instances_,
     then click _New instance_
     1. Is _ubuntu-1404-trusty_ is fine but feel free to use your favorite
        flavor.
     2. I'll use the smallest with a dedicated CPU; n1-standard-1
     3. For fooling around, 10GB of disk can be fine but consider >=300GB for
        reasonable [I/O performance](https://developer.github.com/webhooks/).
     4. Click the button _Edit_;
     5. Add your ssh public key in the _SSH Keys_ section to simplify your life.
     6. Check _Allow HTTP traffic_ and optionally _Allow HTTPS traffic_ if you
        have a certificate.
     7. Click _Network_, _default_.
     8. Open port tcp:9000 in firewall; which will be for the [github's
        webhooks](https://developer.github.com/webhooks/). The actual port can
        be anything.
     9. Follow [online
        instructions](https://cloud.google.com/compute/docs/startupscript#provide_startup_script_contents_directly)
        and copy-paste `startup.sh`.
     10. Go back, click _Save_.
  3. Click _Start_ to boot the VM.
  4. Clear up _.ssh/known_hosts_ with the IP the VM just got.

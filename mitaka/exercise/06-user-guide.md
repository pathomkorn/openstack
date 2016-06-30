# User Guide

* Login OpenStack dashboard with user you just created with admin user

> http://ctrl.podX.openstack.io/

* Set user's timezone
* Create network and subnet
* Create router
* Create security group
* Create key pair
* Create floating IP
* Create Cirros image from below URL

> http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img

* Launch instance
```
Flavor: m1.tiny
Key Pair: your_keypair
Networking: your_network
Post-Creation:
  #!/bin/bash
  date > /tmp/install-date
```
* Login instance with key pair
* Create volume
* Attach volume to instance
* Verify volume in instance
* Logout OpenStack dashboard

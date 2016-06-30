## User Guide
* Login OpenStack dashboard with `admin` username and `your_password` as password

> http://ctrl.podX.openstack.io/

* Set user's timezone
* Create project
* Set project quota
* Create user
* Assign user to project
* Create network, subnet
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

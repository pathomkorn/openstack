# Comprehensive Review
## OS Installation
* Prepare `ctrl.podX.openstack.io` and `comp.podX.openstack.io` nodes follow exercise 03 instruction

## Packstack Installation
* Login `ctrl.podX.openstack.io` as `root` user
```bash
# vi /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
# yum install http://mirror.centos.org/centos/7.2.1511/cloud/x86_64/openstack-mitaka/centos-release-openstack-mitaka-1-3.el7.noarch.rpm
# yum repolist
# yum install openstack-packstack
```

## Packstack Configuration
* Login `ctrl.podX.openstack.io` as `root` user
```bash
# packstack --gen-answer-file=answer.txt
```
* Edit `answer.txt` file as followings
```
CONFIG_SWIFT_INSTALL=n
CONFIG_CEILOMETER_INSTALL=n
CONFIG_AODH_INSTALL=n
CONFIG_GNOCCHI_INSTALL=n
CONFIG_NTP_SERVERS=time.openstack.io
CONFIG_NAGIOS_INSTALL=n
CONFIG_COMPUTE_HOSTS=ctrl.podX.openstack.io,comp.podX.openstack.io
CONFIG_KEYSTONE_ADMIN_PW=your_password
CONFIG_NEUTRON_METERING_AGENT_INSTALL=n
CONFIG_NEUTRON_ML2_TYPE_DRIVERS=vlan
CONFIG_NEUTRON_ML2_TENANT_NETWORK_TYPES=vlan
CONFIG_NEUTRON_ML2_VLAN_RANGES=physnet1:1500:2000
CONFIG_NEUTRON_OVS_BRIDGE_MAPPINGS=physnet1:br-ens224
CONFIG_NEUTRON_OVS_BRIDGE_IFACES=br-ens224:ens224
CONFIG_PROVISION_DEMO=n
```

## OpenStack Deployment
* Login `ctrl.podX.openstack.io` as `root` user
```bash
# packstack --answer-file=answer.txt
```
* Look for `**** Installation completed successfully ******` message
* Configure `br-ex` external bridge
```bash
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens192 /root
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens192 /etc/sysconfig/network-scripts/ifcfg-br-ex
# vi /etc/sysconfig/network-scripts/ifcfg-ens192
DEVICE="ens192"
ONBOOT=yes
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ex
BOOTPROTO=none
```
* Modify `/etc/sysconfig/network-scripts/ifcfg-ens192` file to look like this
```
DEVICE="ens192"
ONBOOT=yes
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ex
```
* Edit `/etc/sysconfig/network-scripts/ifcfg-br-ex` file
```
NAME="br-ex"
DEVICE="br-ex"
TYPE=OVSBridge
```
* Append `/etc/sysconfig/network-scripts/ifcfg-br-ex` file
```
DEVICETYPE=ovs
```
* Refresh network configuration
```bash
# ovs-vsctl add-port br-ex ens192; systemctl restart network
# hostnamectl set-hostname ctrl.podX.openstack.io
```
* Login `comp.podX.openstack.io` as `root` user
```bash
# yum install -y crudini
# crudini --set /etc/neutron/plugins/ml2/openvswitch_agent.ini ovs bridge_mappings physnet1:br-ens224
# systemctl restart neutron-openvswitch-agent
# ovs-vsctl add-br br-ens224
# ovs-vsctl add-port br-ens224 phy-br-ens224
# ovs-vsctl set interface phy-br-ens224 type=patch
# ovs-vsctl set interface phy-br-ens224 options:peer=int-br-ens224
# ovs-vsctl add-port br-int int-br-ens224
# ovs-vsctl set interface int-br-ens224 type=patch
# ovs-vsctl set interface int-br-ens224 options:peer=phy-br-ens224
# cd 
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens224 /root
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens224 /etc/sysconfig/network-scripts/ifcfg-br-ens224
# vi /etc/sysconfig/network-scripts/ifcfg-ens224
DEVICE="ens224"
ONBOOT=yes
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ens224
BOOTPROTO=none
```
* Edit `/etc/sysconfig/network-scripts/ifcfg-br-ens224` file
```
NAME="br-ens224"
DEVICE="br-ens224"
TYPE=OVSBridge
```
* Append `/etc/sysconfig/network-scripts/ifcfg-br-ens224` file
```
DEVICETYPE=ovs
```
* Refresh network configuration
```bash
# ovs-vsctl add-port br-ens224 ens224; systemctl restart network
```

## Test OpenStack Network/Nova (KVM)
* Login Openstack dashboard
* Create pod0X project
* Create pod0X user
* Create public/private network, router
* Create security group, keypair, floating ip
* Launch KVM instances
* Test access to KVM instances
* Remove only KVM instances assigned to `comp.podX.openstack.io`

## Install Docker
* Login `comp.podX.openstack.io` as `root` user
```bash
# yum -y install docker
# vi /etc/sysconfig/docker
OPTIONS='--selinux-enabled -G nova'
# mkdir /etc/systemd/system/docker.service.d
# vi /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://proxy.openstack.io:3128/"
# systemctl daemon-reload
# systemctl enable docker
# systemctl restart docker
# docker run -i -t centos /bin/bash
# exit
```

## Install Nova-Docker
* Login `comp.podX.openstack.io` as `root` user
```bash
# yum -y install git python-pip python-pbr python-docker-py
# git config --global http.proxy http://proxy.openstack.io:3128
# git clone -b stable/mitaka https://github.com/openstack/nova-docker.git /usr/local/src/nova-docker
# cd /usr/local/src/nova-docker
# python setup.py install
```

## Configure Nova
* Login `comp.podX.openstack.io` as `root` user
```bash
# yum -y install crudini
# crudini --set /etc/nova/nova.conf DEFAULT compute_driver novadocker.virt.docker.DockerDriver
# mkdir -p /etc/nova/rootwrap.d
# cp -p /usr/local/src/nova-docker/etc/nova/rootwrap.d/docker.filters /etc/nova/rootwrap.d/docker.filters
# chgrp -R nova /etc/nova/rootwrap.d
# systemctl restart openstack-nova-compute
```

## Configure Glance
* Login `ctrl.podX.openstack.io` as `root` user
```bash
crudini --set /etc/glance/glance-api.conf DEFAULT container_formats ami,ari,aki,bare,ovf,ova,docker
openstack-service restart glance
```

## Test OpenStack Network/Nova (Docker)
* Login `comp.podX.openstack.io` as `root` user
```bash
# scp root@ctrl.podX.openstack.io:/root/keystonerc_admin /root
# source /root/keystonerc_admin
# docker pull larsks/thttpd
# docker save larsks/thttpd | glance image-create --name larsks/thttpd --container-format docker --disk-format raw
```
* Promote larsks/thttpd image as public from admin project
* Update image for KVM/docker hypervisor types
```bash
# glance image-update ${docker_image_id} --property architecture=x86_64 --property hypervisor_type=docker
# glance image-update ${qcow2_image_id} --property architecture=x86_64 --property hypervisor_type=qemu
```
* Test provisioning using existing network / floating ip
  * Try cirros, ubuntu and centos docker images

# Multiple Nodes Deployment
## Packstack Installation
* Login `ctrl.podX.openstack.io` as `root` user
* Install `packstack` utility
```bash
# yum install http://mirror.centos.org/centos/7.2.1511/cloud/x86_64/openstack-mitaka/centos-release-openstack-mitaka-1-3.el7.noarch.rpm
# yum repolist
# yum install openstack-packstack
```

## Packstack Configuration
* Generate default packstack answer file, `answer.txt`
```bash
# packstack --gen-answer-file=answer.txt
```
* Edit `answer.txt` file as followings
```
CONFIG_CEILOMETER_INSTALL=n
CONFIG_AODH_INSTALL=n
CONFIG_GNOCCHI_INSTALL=n
CONFIG_HEAT_INSTALL=y
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
CONFIG_SWIFT_STORAGE_SIZE=20G
CONFIG_PROVISION_DEMO=n
```

## OpenStack Deployment
* Run packstack with `answer.txt` file
```bash
# packstack --answer-file=answer.txt
```
* Wait until packstack finish then look for `**** Installation completed successfully ******` message

## Post Installation

Below instruction convert ens192 interface to OpenvSwitch port then plug into br-ex bridge. It also move IP address of controller node from ens192 interface to br-ex bridge.

![OpenStack networking](http://image.slidesharecdn.com/openstacknetworking-vlangre-131215080350-phpapp01/95/open-stack-networking-vlan-gre-7-638.jpg?cb=1387759997)

* Backup `ens192` external interface configuration file
```bash
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens192 /root
```
* Create external bridge interface configuration file
```bash
# cp -p /etc/sysconfig/network-scripts/ifcfg-ens192 /etc/sysconfig/network-scripts/ifcfg-br-ex
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
* Plug external interface to external bridge and move IP address to external bridge
```bash
# ovs-vsctl add-port br-ex ens192; systemctl restart network
```
* Verify external bridge configuration
```bash
# ovs-vsctl show
    Bridge br-ex
        Port br-ex
            Interface br-ex
                type: internal
        Port "ens192"
            Interface "ens192"
```
* Reconfigure hostname
```bash
# hostnamectl set-hostname ctrl.podX.openstack.io
```

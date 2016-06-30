# Single Node Deployment
## Packstack Installation
* Login `controller node` as `root` user
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
CONFIG_SWIFT_INSTALL=n
CONFIG_CEILOMETER_INSTALL=n
CONFIG_AODH_INSTALL=n
CONFIG_GNOCCHI_INSTALL=n
CONFIG_NTP_SERVERS=ntp.ibmcloud.com
CONFIG_NAGIOS_INSTALL=n
CONFIG_KEYSTONE_ADMIN_PW=your_password
CONFIG_NEUTRON_METERING_AGENT_INSTALL=n
CONFIG_PROVISION_DEMO=n
```

## OpenStack Deployment
* Run packstack with answer.txt file
```bash
# packstack --answer-file=answer.txt
```
* Wait until packstack finish then look for `**** Installation completed successfully ******` message

## OpenStack Verification
* Source OpenStack RC file
```bash
cat /root/keystonerc_admin
source /root/keystonerc_admin
```
* Verify OpenStack status
```bash
# openstack-status
== Nova services ==
openstack-nova-api:                     active
openstack-nova-compute:                 active
openstack-nova-network:                 inactive  (disabled on boot)
openstack-nova-scheduler:               active
openstack-nova-cert:                    active
openstack-nova-conductor:               active
openstack-nova-console:                 inactive  (disabled on boot)
openstack-nova-consoleauth:             active
openstack-nova-xvpvncproxy:             inactive  (disabled on boot)
== Glance services ==
openstack-glance-api:                   active
openstack-glance-registry:              active
== Keystone service ==
openstack-keystone:                     inactive  (disabled on boot)
== Horizon service ==
openstack-dashboard:                    active
== neutron services ==
neutron-server:                         active
neutron-dhcp-agent:                     active
neutron-l3-agent:                       active
neutron-metadata-agent:                 active
neutron-openvswitch-agent:              active
== Cinder services ==
openstack-cinder-api:                   active
openstack-cinder-scheduler:             active
openstack-cinder-volume:                active
openstack-cinder-backup:                inactive  (disabled on boot)
== Support services ==
mysqld:                                 inactive  (disabled on boot)
openvswitch:                            active
dbus:                                   active
target:                                 active
rabbitmq-server:                        active
memcached:                              active
== Keystone users ==
+----------------------------------+---------+---------+-------------------+
|                id                |   name  | enabled |       email       |
+----------------------------------+---------+---------+-------------------+
| b9bedbf6a803462fb4d1edc131be40a1 |  admin  |   True  |   root@localhost  |
| a10651cf924a4ad189966525f15de432 |  cinder |   True  |  cinder@localhost |
| ae103b0906244f2e918e4cbb67134d02 |  glance |   True  |  glance@localhost |
| b960becc1c1b4509b7824e2928fd789f | neutron |   True  | neutron@localhost |
| 8dc317989a9c46db80eb4e73c45c8959 |   nova  |   True  |   nova@localhost  |
+----------------------------------+---------+---------+-------------------+
== Glance images ==
+----+------+
| ID | Name |
+----+------+
+----+------+
== Nova managed services ==
+----+------------------+-------------------+----------+---------+-------+----------------------------+-----------------+
| Id | Binary           | Host              | Zone     | Status  | State | Updated_at                 | Disabled Reason |
+----+------------------+-------------------+----------+---------+-------+----------------------------+-----------------+
| 4  | nova-cert        | ctrl.ibmcloud.com | internal | enabled | up    | 2016-05-11T09:41:34.000000 | -               |
| 5  | nova-consoleauth | ctrl.ibmcloud.com | internal | enabled | up    | 2016-05-11T09:41:28.000000 | -               |
| 6  | nova-scheduler   | ctrl.ibmcloud.com | internal | enabled | up    | 2016-05-11T09:41:32.000000 | -               |
| 7  | nova-conductor   | ctrl.ibmcloud.com | internal | enabled | up    | 2016-05-11T09:41:36.000000 | -               |
| 9  | nova-compute     | ctrl.ibmcloud.com | nova     | enabled | up    | 2016-05-11T09:41:31.000000 | -               |
+----+------------------+-------------------+----------+---------+-------+----------------------------+-----------------+
== Nova networks ==
+--------------------------------------+---------------+------+
| ID                                   | Label         | Cidr |
+--------------------------------------+---------------+------+
+--------------------------------------+---------------+------+
== Nova instance flavors ==
+----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
| ID | Name      | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
+----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
| 1  | m1.tiny   | 512       | 1    | 0         |      | 1     | 1.0         | True      |
| 2  | m1.small  | 2048      | 20   | 0         |      | 1     | 1.0         | True      |
| 3  | m1.medium | 4096      | 40   | 0         |      | 2     | 1.0         | True      |
| 4  | m1.large  | 8192      | 80   | 0         |      | 4     | 1.0         | True      |
| 5  | m1.xlarge | 16384     | 160  | 0         |      | 8     | 1.0         | True      |
+----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
== Nova instances ==
+----+------+-----------+--------+------------+-------------+----------+
| ID | Name | Tenant ID | Status | Task State | Power State | Networks |
+----+------+-----------+--------+------------+-------------+----------+
+----+------+-----------+--------+------------+-------------+----------+
ovs-vsctl show
    Bridge br-ex
        Port br-ex
            Interface br-ex
                type: internal
```
cd /etc/sysconfig/network-scripts
cp -p ifcfg-ens192 /root
cp -p ifcfg-ens192 ifcfg-br-ex
vi ifcfg-ens192
DEVICE="ens192"
ONBOOT=yes
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ex
vi ifcfg-br-ex
NAME="br-ex"                # edit
DEVICE="br-ex"                # edit
TYPE=OVSBridge                # edit
DEVICETYPE=ovs                # add
ovs-vsctl add-port br-ex ens192; systemctl restart network
ovs-vsctl show
    Bridge br-ex
        Port br-ex
            Interface br-ex
                type: internal
        Port "ens192"
            Interface "ens192"
hostnamectl set-hostname ctrl.pod0X.isd.th.ibm.com
```

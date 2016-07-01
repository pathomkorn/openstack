# Troubleshooting
## OpenStack Liberty Installation
* Run packstack to install `OpenStack Liberty` in multiple compute nodes (excercise 07)
* Login OpenStack dashboard
* Open `Network Topology` tab

## Solve Cinder Error (only in Liberty version)
```bash
# yum install -y crudini
# crudini --set /etc/cinder/cinder.conf keystone_authtoken auth_uri http://ctrl.podX.openstack.io:5000
# openstack-service restart cinder
```

## OpenStack Liberty Configuration
* Create podXa and podxb projects
* Create podXa & podXb users
* Assign admin to both projects with admin privilege
* Assign podXa user to podXa projects with member privilege
* Assign pod0Xb user to pod0Xb projects with member privilege
* Create external network
  * 192.168.X.0/24
  * No DHCP
  * 192.168.X.51-192.168.X.81
* Login to pod0Xa user
* Create production network
* Create test network
* Create router with 3 network connections
* Register cirros public images
* Launch instance cirros00 to test network
  * Grant Ping/SSH
* Launch instance cirros01 and cirros02 to production network
  * Grant Ping
* Run date > /tmp/install-date command during provision
* NAT all 3 instances to external network
* Test access 3 instances

## Solve Neutron Error (On Compute Nodes Only)
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
# ovs-vsctl add-port br-ens224 ens224
# systemctl restart network
```

## Debug Neutron
* Get neutron net ID
```bash
# neutron net-list
```
* Create neutron probe device
```bash
# neutron-debug --config-file /etc/neutron/l3_agent.ini probe-create ${net_id}
```
* Get neutron probe and router ID
```bash
# ip netns
```
* Test Network
```bash
# ip netns exec ${qrouter_id} ping ${neutron_probe}
```

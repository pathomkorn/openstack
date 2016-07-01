# OpenStack Docker Integration
* Install OpenStack Mitaka
```bash
# packstack --allinone --os-swift-instal=n --os-ceilometer-install=n --os-aodh-install=n --os-gnocchi-install=n --nagios-install=n --keystone-admin-passwd=your_password --os-neutron-metering-agent-install=n --provision-demo=n
```
* Install Docker
```bash
# yum -y install docker-io
# vi /etc/sysconfig/docker
OPTIONS='--selinux-enabled -G nova'
# mkdir /etc/systemd/system/docker.service.d
# vi /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://proxy.openstack.io:3128/"
# systemctl daemon-reload
# systemctl enable docker
# systemctl restart docker
# ls -l /var/run/docker.sock
# docker pull centos
# docker run -i -t centos /bin
```
* Install Nova-Docker
```bash
# yum -y install git python-pip python-pbr python-docker-py
# git config --global http.proxy http://proxy.openstack.io:3128
# git clone -b stable/mitaka https://github.com/openstack/nova-docker.git /sources/nova-docker
# cd /sources/nova-docker
# python setup.py install
```
* Configure Nova
```bash
# crudini --set /etc/nova/nova.conf DEFAULT compute_driver novadocker.virt.docker.DockerDriver
# mkdir -p /etc/nova/rootwrap.d
# cp -p /sources/nova-docker/etc/nova/rootwrap.d/docker.filters /etc/nova/rootwrap.d/docker.filters
# chgrp -R nova /etc/nova/rootwrap.d
# openstack-service restart nova
```
* Configure Glance
```bash
# crudini --set /etc/glance/glance-api.conf DEFAULT container_formats ami,ari,aki,bare,ovf,ova,docker
# openstack-service restart glance
```
* Testing
```bash
# source /root/keystonerc_admin
# docker pull larsks/thttpd
# docker save larsks/thttpd | glance image-create --name larsks/thttpd --container-format docker --disk-format raw
```
* Test provisioning
  * Try cirros, ubuntu and centos docker images

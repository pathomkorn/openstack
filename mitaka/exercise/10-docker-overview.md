# Docker Overview
## Install Docker
* Login `comp.podX.openstack.io` as `root` user
```bash
# yum -y install docker
```

## Use Docker
* Review docker version
```bash
# docker version
```
* Setup proxy server from docker (optional)
```bash
# mkdir /etc/systemd/system/docker.service.d
# vi /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://proxy.openstack.io:3128/"
# systemctl daemon-reload
```
* Start docker service
```bash
# systemctl start docker
# systemctl enable docker
```
* Review docker version
```bash
# docker version
```
* Run first docker image
```bash
# docker images
# docker pull busybox
# docker images
# docker ps
# docker run -i -t centos /bin/bash
# cat /etc/centos-release
```
* Run second docker image on another tty
```bash
# docker run -i -t ubuntu /bin/bash
# cat /etc/os-release
```
* Verify docker processes on another tty
```bash
# docker ps
```

![Docker logo](https://docs.mldb.ai/doc/builtin/img/logo_docker.png)

## Build your own image
### Option 1: Using Dockerfile (More Effective, Continuous Integration & Deployment Process)
```bash
# mkdir -p /myimage1/v1
# cd /myimage1/v1
# vi Dockerfile
FROM busybox
MAINTAINER yourname <yourname@yourdomain>
RUN date > /tmp/build-date
# docker build --tag myimage1/v1 .
# docker images
# docker run -i -t myimage1/v1 sh
# mkdir -p /myimage1/v2
# cd /myimage1/v2
# vi Dockerfile
FROM myimage1/v1
MAINTAINER yourname <yourname@yourdomain>
RUN date > /tmp/build-date-v2
# docker build --tag myimage1/v2 .
# docker images
# docker run -i -t myimage1/v2 sh
```
### Option 2: Using docker commit
```bash
# docker run -it busybox sh
# date > /tmp/build-date
```
* On another tty
```bash
# docker ps
# docker commit ${container_id} myimage2/v1
# docker images
```

# Hardware Requirements

You may select single node or multiple node topology for your practice.

## Single Node Topology
### OpenStack KVM/Docker All-in-One Node
* Processor and memory
  * Minimum: 1 processor and 3 GB memory
  * Recommended: 2 processors and 2 GB memory plus additional memory capable to run workloads
* Network interface card
  * 1 NIC card
* Hostname: `ctrl.podX.openstack.io`

## Multiple Nodes Topology
### OpenStack Controller + KVM/Docker Compute Node
* Processor and memory
  * Minimum: 1 processor and 3 GB memory
  * Recommended: 2 processors and 2 GB memory plus additional memory capable to run workloads
* Network interface card
  * 2 NIC cards
    * First NIC card for external network & management interface
    * Second NIC card for internal network
* Hostname: `ctrl.podX.openstack.io`

### OpenStack KVM/Docker Compute Node
* Processor and memory
  * Minimum: 1 processor and 2 GB memory
  * Recommended: 2 processors and 2 GB memory plus additional memory capable to run workloads
* Network interface card
  * 2 NIC cards
    * First NIC card for management interface
    * Second NIC card for internal network
* Hostname: `comp.podX.openstack.io`

> VMware or KVM hypervisor maybe used for running OpenStack controller and OpenStack compute node.

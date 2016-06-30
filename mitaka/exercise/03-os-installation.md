# OS Installation
* On both `ctrl.podX.openstack.io` and `comp.podX.openstack.io`, boot from CentOS 7.2 PXE/DVD to launch installation image
  * Select `Install CentOS 7.2` from boot menu
    * DATE & TIME: Set timezone and NTP server
    * SOFTWARE SELECTION: Minimal install
    * INSTALLATION DESTINATION
      * /dev/sda1: /boot: 500 MiB
      * /dev/mapper/vg_root-lv_swap: swap: 8192 MiB
      * /dev/mapper/vg_root-lv_root: /: all remaining spaces
    * NETWORK & HOSTNAME
      * Controller node
        * Host name: `ctrl.podX.openstack.io`
        * External NIC: ens192: On: Auto connect
          * IPv4: Addr 192.168.X.21/24: GW 192.168.X.1: DNS 192.168.Y.21: Search podX.openstack.io
          * IPv6: Link-Local Only
        * Internal NIC: ens224: On: Auto connect
          * IPv4: Addr 192.168.X+100.21/24
          * IPv6: Link-Local Only
      * Compute node
        * Host name: `comp.podX.openstack.io`
        * External NIC: ens192: On: Auto connect
          * IPv4: Addr 192.168.X.22/24: GW 192.168.X.1: DNS 192.168.Y.21: Search podX.openstack.io
          * IPv6: Link-Local Only
        * Internal NIC: ens224: On: Auto connect
          * IPv4: Addr 192.168.X+100.22/24
          * IPv6: Link-Local Only
        * ROOT PASSWORD: set
      * Reboot

# OS Post-Installation
* Login both `ctrl.podX.openstack.io` and `comp.podX.openstack.io` as `root` user
  * Test network connectivity
```bash
# ping ctrl.podX.openstack.io
# ping comp.podX.openstack.io
# ping 192.168.X.1
# ping 192.168.X+100.1
```
  * Modify `/etc/hosts` file
```bash
192.168.X.21 ctrl.podX.openstack.io ctrl
192.168.X.22 comp.podX.openstack.io comp
```
  * Disable selinux by changing SELINUX setting in `/etc/selinux/config` file (optional)
```bash
SELINUX=disabled
```
  * Disable firewalld service (optional)
```bash
# systemctl stop firewalld
# systemctl disable firewalld
```
  * Disable NetworkManager service (required for packstack command)
```bash
# systemctl stop NetworkManager
# systemctl disable NetworkManager
```
  * Reboot
```bash
# systemctl reboot
```
* Login both `ctrl.podX.openstack.io` and `comp.podX.openstack.io`
  * Modify proxy setting in `/etc/yum.conf` if any (optional)
```bash
proxy=http://proxy.openstack.io:3128/
```
  * Test YUM command
```bash
# yum repolist
```
  * Update CentOS to latest version
```bash
# yum update -y
```
  * Install VM tools and bash completion packages (optional)
```bash
# yum install -y open-vm-tools bash-completion
```
  * Power off
```bash
# systemctl poweroff
```
  * Make a backup or snapshot of both `ctrl.podX.openstack.io` and `comp.podX.openstack.io` (optional)
  * Power on both `ctrl.podX.openstack.io` and `comp.podX.openstack.io`

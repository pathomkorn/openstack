# OS Installation
* Boot from CentOS 7.2 PXE/DVD to launch installation image
  * Select "Install CentOS 7.2" from boot menu
    * DATE & TIME: Set timezone and NTP server
    * SOFTWARE SELECTION: Minimal install
    * INSTALLATION DESTINATION
      * /dev/sda1: /boot: 500 MiB
      * /dev/mapper/vg_root-lv_swap: swap: 8192 MiB
      * /dev/mapper/vg_root-lv_root: /: all remaining spaces
    * NETWORK & HOSTNAME
      * Controller node
        * Host name: ctrl.podX.ibmcloud.com
        * External NIC: ens192: On: Auto connect
          * IPv4: Addr 192.168.X.21/24: GW 192.168.X.1: DNS 192.168.Y.21: Search podX.ibmcloud.com,trn.ibmcloud.com
          * IPv6: Link-Local Only
        * Internal NIC: ens224: On: Auto connect
          * IPv4: Addr 192.168.X+100.21/24
          * IPv6: Link-Local Only
      * Compute node
        * Host name: comp.podX.ibmcloud.com
        * External NIC: ens192: On: Auto connect
          * IPv4: Addr 192.168.X.22/24: GW 192.168.X.1: DNS 192.168.Y.21: Search podX.ibmcloud.com,trn.ibmcloud.com
          * IPv6: Link-Local Only
        * Internal NIC: ens224: On: Auto connect
          * IPv4: Addr 192.168.X+100.22/24
          * IPv6: Link-Local Only
        * ROOT PASSWORD: set
      * Reboot

# OS Post-Installation
* Login both controller and compute nodes
  * Test network connectivity
    * ping ctrl.podX.ibmcloud.com
    * ping comp.podX.ibmcloud.com
    * ping 192.168.X.1
    * ping 192.168.X+100.1
  * Add host record itself in /etc/hosts file
  * Disable selinux (optional)
  * Disable firewalld service (optional)
  * Disable NetworkManager service
  * systemctl reboot
* Login both controller node and compute node
  * Add proxy=http://proxy.ibmcloud.com:3128/ to /etc/yum.conf (optional)
  * yum repolist
  * yum update -y
  * yum install -y open-vm-tools bash-completion
  * systemctl reboot
* Make a backup or snapshot of both controller and compute nodes (optional)

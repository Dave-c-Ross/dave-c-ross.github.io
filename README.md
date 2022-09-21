## Linux Commands

### Network

Add new ip to connection
`sudo nmcli con mod 'Wired connection 5' +ipv4.addresses '10.1.198.102/28' ipv4.gateway '10.1.198.110' ipv4.dns '10.1.196.40' ipv4.ignore-auto-dns yes`

Get Listening Apps
`sudo netstat -tupln`

### SELinux Management Commands

List port managed by SELinux
`sudo semanage port -l | grep http_port_t`

Grant port in SELinux
`sudo semanage port -a -t http_port_t -p tcp 6443`


### FirewallD Manahement Commands

List FirewallD rules
`sudo firewall-cmd --list-all`

Add rule to FirewallD
`sudo firewall-cmd --zone=public --add-port=9443/tcp --permanent`

Reload FirewallD
`sudo firewall-cmd --reload`

### Debug Openshift

Get Workload Resources
`sudo crictl`
## Table of Content
- [SELinux Management Commands](#SELinux-Management-Commands)
- [FirewallD Manahement Commands](#FirewallD-Manahement-Commands)

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
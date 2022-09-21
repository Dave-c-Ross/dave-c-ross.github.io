# Snippet

List port managed by SELinux
`sudo semanage port -l | grep http_port_t`

Grant port in SELinux
`sudo semanage port -a -t http_port_t -p tcp 6443`
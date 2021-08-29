# Network Management


```bash
# select ip address with netaddr
ansible-playbook -i inventory.ini tasks8.yml

# increment address with netaddr
ansible-playbook -i inventory.ini tasks9.yml

```

## Netork interface config

create /etc/network direcotory and interface file in the web_server hosts
```bash
cd /etc
mkdir network
touch network/interfaces
```


# Ansible + Docker

## Docker Container Run

```bash
cd docker-ansible
docker-compose up -d
```

## Setup Ansible Container
```bash
docker exec -it ansible /bin/sh

#python3 as default
apt-get update

update-alternatives --install /usr/bin/python python /usr/bin/python3 1

#install pip
apt install -y python3-pip	
apt install -y vim

# ssh target01 - target05 from the ansible container

ssh target01    # yes
exit
ssh target02    # yes
exit
```

## Test Run Ansible
```bash
docker exec -it ansible /bin/sh
ansible localhost -m ping
```
see the IP addresses of target hosts
```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target01

docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target02
```

add the host ip in the hosts file
```bash
vi /etc/ansible/hosts

...
#/etc/ansible/hosts

#db-[99:101]-node.example.com
172.18.0.4
```

run the test code
```bash
# ping
ansible 172.18.0.4 -m ping

# test msg
ansible 172.18.0.4 -a "echo test"
```
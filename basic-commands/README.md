# Ansible Basic Commands

## configure hosts

```bash
# edit hosts file
vi /etc/ansible/hosts
```

connect the target host
```bash
ssh 172.18.0.4
exit
```
add the host ip in the hosts file
```bash
vi /etc/ansible/hosts

...
# Here's another example of host ranges, this time there are no
# leading 0s:

#db-[99:101]-node.example.com
172.18.0.4
```
run the test code
```bash
ansible 172.18.0.4 -a "echo test"

# if you didn't not connect ssh
ansible 172.18.0.4 -a "echo test" --extra-vars "ansible_user=root ansible_password=p@ssword123"

```

## Execute Methods
- Ad-hoc execute
- Playbook execute

## Ad-hoc execute

```bash
# ping
ansible 172.18.0.4 -m ping

# test msg
ansible 172.18.0.4 -a "echo test"

# reboot
ansible 172.18.0.4 -a "mkdir testomato"
```

## Playbook

```bash
mkdir ansible-playbook
vi site.yml
```

```yaml
#ansible-playbook/site.yml
- hosts: 172.18.0.4
  gather_facts: yes
  remote_user: root
  tasks:
    - name: " ✨create a folder ✨ "
      ansible.builtin.file:
        path: /root/test
        state: directory
        mode: '0755'
      tags:
        - create-folder
```

```bash
ansible-playbook site.yml
```
[More about Playbook](https://github.com/yuyatinnefeld/ansible/tree/master/basic-commands/learn-playbook)

## Inventory
Ansible works against multiple managed nodes or “hosts” in your infrastructure at the same time, using a list or group of lists known as inventory. Once your inventory is defined, you use patterns to select the hosts or groups you want Ansible to run against.

create an example inventory.ini
```bash
vi inventory.ini

#inventory.ini
[all]
web1 ansible_ssh_host=target01 #ex 172.18.0.3
web2 ansible_ssh_host=target02 #ex 172.18.0.5
db1 ansible_ssh_host=target03 #ex 172.18.0.2
db2 ansible_ssh_host=target04 #ex 172.18.0.6
db3 ansible_ssh_host=target05 #ex 172.18.0.7

[product_01]
web1
db1

[product_02]
web2
db2

[web_servers]
web1
web2

[db_servers]
db1
db2

[backup]
db3

[web_servers:vars]
web_file=/root/web-data123.csv
```

```bash
cd ansible-playbook
ansible-playbook -i ../inventory.ini tasks1.yml
```


## Roles
[More about Roles](https://github.com/yuyatinnefeld/ansible/tree/master/basic-commands/learn-roles)

## Secrets
[More about Secrets](https://github.com/yuyatinnefeld/ansible/tree/master/basic-commands/learn-secrets)

## Network Management
[More about Network](https://github.com/yuyatinnefeld/ansible/tree/master/basic-commands/learn-network)
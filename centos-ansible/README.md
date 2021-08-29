# Ansible + CentOS

## Setup Ansible

### Install Ansible
```bash
sudo yum install epel-release

sudo yum install ansible
```

### Configure Ansible Host
```bash
sudo vi /etc/ansible/hosts

#/etc/ansible/hosts
[group_name]
alias ansible_ssh_host=your_server_ip

ssh root@your_server_ip

# /etc/ansible/hosts
[servers]
host1 ansible_ssh_host=192.0.2.1
host2 ansible_ssh_host=192.0.2.2
host3 ansible_ssh_host=192.0.2.3

sudo mkdir /etc/ansible/group_vars

sudo vi /etc/ansible/group_vars/servers

#/etc/ansible/group_vars/servers.yml
---
ansible_ssh_user: sammy
```

### Using Ansible Commands

```bash
ansible -m ping all

ansible -m ping servers

ansible -m ping host1

ansible -m ping host1:host2
```
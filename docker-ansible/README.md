<h1 align="center">Ansible + Docker </h1> <br>

## Info
- https://github.com/Naoto92X82V99/docker-ansible.git

## Topics

### 1. [Initial-Setup](#Initial-Setup)
### 2. [Create-Playbook-Tasks](#Create-Playbook-Tasks)
### 3. [Roles](#Roles)
### 4. Working with Secrets
### 5. Network Management


## Initial-Setup

git clone

```bash
git clone https://github.com/Naoto92X82V99/docker-ansible.git
```

container run
```bash
docker-compose up -d
```

access ansible container
```bash
docker exec -it ansible /bin/sh

#python3 as default
update-alternatives --install /usr/bin/python python /usr/bin/python3 1

#install pip
apt install python3-pip	

# ssh target01 and target02 from the ansible container

ssh target01    # yes
exit
ssh target02    # yes
exit
```

```bash
docker exec -it ansible /bin/sh
ansible localhost -m ping
```

ansible cmd for target01 and target02

```bash
ansible-playbook -i inventry.ini playbook.yml
```

check the hoge files in the target01 and 02
```bash
ssh target01
ls
exit
ssh target02
ls
```

## Create-Playbook-Tasks


### tags
use tags for the specific commands
```bash
ansible-playbook -i inventry.ini tasks1.yml --tags create-file
ansible-playbook -i inventry.ini tasks1.yml --tags delete-file
```

skip only create-file tag
```bash
ansible-playbook -i inventry.ini tasks1.yml --skip-tags create-file
```

### variables
use the dynamic parameter (e.g {{file_state}} = touch)
```bash
ansible-playbook -i inventry.ini tasks1.yml -e file_state=touch
```

```bash
- hosts: product_02
  tasks:
    - name: " ✨ create a file for the product 02 ✨ "
      file:
        dest: /root/product2-data
        state: '{{file_state}}'
```

### variables by inventory
```bash
# inventory.ini
[web:vars]
web_file=/root/web-data123
```

```bash
# tasks1.yml
- hosts: web_servers
  ...
        dest: '{{web_file}}'
```
## create and delete files
```bash
ansible -m debug -i inventory.ini tasks1.yml
ansible -m debug -i inventory.ini tasks2.yml
```

## create directory
```bash
ansible -m debug -i inventory.ini tasks1.yml
```

## templates 
copy the files from the ansible host to the guest hosts with templates


```bash
# create the teamplates folder and files
mkdir templates
touch temp555.txt
```

```bash
# templates/temp555.txt will be saved to /root/temp555_saved.txt
- hosts: web_servers
  tasks:
    - name: " 🚀✨ create files 🚀✨ "
      template:
        src: templates/temp555.txt
        dest: /root/temp555_saved.txt
      tags:
        - create
```

## debugging
```bash
ansible -m debug -i inventory.ini -a "var=hostvars['web2']" web1
```
or create playbook for the debugging
```bash
ansible-playbook -i inventory.ini tasks4.yml
```

## install and uninstall packeges
```bash
#install
ansible-playbook -i inventory.ini tasks5.yml -e file_state=touch -e pkg_state=latest

#uninstall
ansible-playbook -i inventory.ini tasks5.yml -e file_state=absent -e pkg_state=absent
```

## looping task with the variables
```bash
ansible-playbook -i inventory.ini tasks6.yml -e file_state=touch
```
looping with the templates
```bash
ansible-playbook -i inventory.ini tasks7.yml --tags create
```

## Roles

### run tasks/create_user.yml without the role
create user
```bash
ansible-galaxy init create_user
```

create playbooks
```bash
touch create_play.yml
touch create_role.yml
touch tasks/create_user.yml
```

create bashrc.j2
```bash
touch templates/bashrc.js
```

create a ssh key (cloud_key) in the ansible server
```bash
docker exec -it ansible sh
ssh-keygen => cloud_key
```

run the create_play.yml
```bash
docker exec -it ansible sh
ansible-playbook -i inventory.ini create_play.yml
```

### run user_create/tasks/create_user.yml with the role

overwrite the setup into the role "create_user"

```bash
# copy crate_user.yml into the role "create_user"
cp tasks/create_user.yml create_user/tasks/main.yml

# copy templates file into the role "create_user"
cp templates/bashrc.j2 create_user/templates/
```

```bash
# now you can use the role (create_user)
ansible-playbook -i inventory.ini create_role.yml
```



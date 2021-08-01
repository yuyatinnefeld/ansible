<h1 align="center">Ansible + Docker </h1> <br>

## Info
- https://github.com/Naoto92X82V99/docker-ansible.git

## Topics

### 1. [Initial-Setup](#Initial-Setup)
### 2. [Create-Playbook-Tasks](#Create-Playbook-Tasks)
### 3. [Roles](#Roles)
### 4. [Secrets](#Secrets)
### 5. [Network-Management](#Network-Management)


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

```yml
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

```yml
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

```yml
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

```yml
# create_play.yml
---
- hosts: product_02
  vars:
    user_name: root
    user_state: present
    ssh_key: ~/.ssh/cloud_key.pub
  tasks:
     - include_tasks: tasks/create_user.yml


# create_role.yml
---
- hosts: product_02
  tasks:
     - include_role:
         name: create_user
       vars:
         user_name: root
         user_state: present
         ssh_key: ~/.ssh/cloud_key.pub

# tasks/create_user.yml
---
- name: " 🙌 create user on remote host 🙌" 
  user:
    name: '{{user_name}}'
    state: '{{user_state}}'
    remove: yes
    shell: /bin/bash
    groups: root
    append: yes
    password:

- name: " 🔑 publish local ssh public key for remote login 🔑 "
  authorized_key:
    user: '{{user_name}}'
    state: '{{user_state}}'
    key: "{{ lookup('file', '{{ssh_key}}') }}"

- name: " 🦾 add bashrc to include host and user 🦾 "
  template:
    dest: '~{{user_name}}/.bashrc'
    src: templates/bashrc.j2    

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

### configure the defaults variables

1. delete user_state:present from the create_role.yml

# create_role.yml

```yml
---
- hosts: product_02
  tasks:
     - include_role:
         name: create_user
       vars:
         user_name: root
         ssh_key: ~/.ssh/cloud_key.pub
```

2. add defaults/main.yml the user_state

```bash
sudo vi create_user/defaults/main.yml
```

```bash
---
# defaults file for create_user
user_state: present
```

```bash
# as default present
ansible-playbook -i inventory.ini create_role.yml
```

```bash
# you can also use absent
ansible-playbook -i inventory.ini create_role.yml -e user_statte=absent
```

### use flexible user name

add user_name
```bash
vi create_user/defaults/main.yml
```

```bash
---
# defaults file for create_user
user_state: present
user_name: default
```

update roles README.md
```bash
vi create_user/README.md
```

```bash
...
# Define the user you would like to create
user_name: default

# Define the user state present or absent
user_state: present
```

now you can create the user flexible
```bash
ansible-playbook -i inventory.ini create_role.yml -e user_name=yuya
```

check the new user in the home directory
```bash
ssh target02
cd /home
ls
```

delete user
```bash
ansible-playbook -i inventory.ini create_role.yml -e user_name=yuya -e user_state=absent
```

### import role from ansible galaxy
```bash
# login with the github user
ansible-galaxy login

# search roles in the galax
ansible-galaxy search create_user

# install an example role (ex. gortc.nginx)
ansible-galaxy install <company>.<product> -p ./
ansible-galaxy install gortc.nginx -p ./

```

## Secrets

you can use ansible vault file to encrypt the secret information

```bash
mkdir group_vars
mkdir group_vars/all
touch group_vars/all/vars
touch group_vars/all/vault
```

```bash
# group_vars/all/vars
password: '{{vault_special_secretpwd}}'
```

```bash
# group_vars/all/vault
vault_special_secretpwd: thisIsMyPassword
```

```bash
cd group_vars/all
more vars
password: '{{vault_special_secretpwd}}'
```

```bash
ansible-vault encrypt vault
```

### encrypt
```bash
# ansible-vault encrypt vault (e.g. hallo)
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

```bash
# more vault

$ANSIBLE_VAULT;1.1;AES256
65613437643434336264643636343861343661326536646438366165346262313765353865633733
3238663234353066353131336235383833366661643631300a313432363233363534656638613033
62306561663933316435326536396562616562356532373639326331326466326364356632393564
3566383166333039660a636537306164643166313666353736663332376164626365313038303734
37363461366263313466376637656265333463643336373162656666373863633566653964393634
3332643666363661653565376139393931303030376335323064
```

### edit
```bash
ansible-vault edit vault
```

after the vault setup you must use --ask-vault-pass for the playbook run
```bash
ansible-playbook -i inventory.ini tasks1.yml --ask-vault-pass
```

### decrypt vault
```bash
ansible-vault decrypt vault
```

## Network-Management


```bash
# select ip address with netaddr
ansible-playbook -i inventory.ini tasks8.yml

# increment address with netaddr
ansible-playbook -i inventory.ini tasks9.yml

```

### Netork interface config

create /etc/network direcotory and interface file in the web_server hosts
```bash
cd /etc
mkdir network
touch network/interfaces
```


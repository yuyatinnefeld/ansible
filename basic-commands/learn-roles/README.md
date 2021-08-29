# Roles

## run tasks/create_user.yml without the role
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

## run user_create/tasks/create_user.yml with the role

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

## configure the defaults variables

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

## use flexible user name

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

## import role from ansible galaxy
```bash
# login with the github user
ansible-galaxy login

# search roles in the galax
ansible-galaxy search create_user

# install an example role (ex. gortc.nginx)
ansible-galaxy install <company>.<product> -p ./
ansible-galaxy install gortc.nginx -p ./

```

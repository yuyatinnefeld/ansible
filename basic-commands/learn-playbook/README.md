# Playbook

## tags
use tags for the specific commands
```bash
cd ansible-playbook
ansible-playbook -i ../inventory.ini tasks1.yml
ansible-playbook -i ../inventory.ini tasks1.yml --tags create-file
ansible-playbook -i ../inventory.ini tasks1.yml --tags delete-file
```

skip only create-file tag
```bash
ansible-playbook -i ../inventory.ini tasks1.yml --skip-tags create-file
```

### variables
use the dynamic parameter (e.g {{file_state}} = touch)
```bash
ansible-playbook -i ../inventory.ini tasks1.yml -e file_state=touch
```

```yml
- hosts: product_02
  tasks:
    - name: " ✨ create a file for the product 02 ✨ "
      file:
        dest: /root/product2-data
        state: '{{file_state}}'
```

## variables by inventory
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

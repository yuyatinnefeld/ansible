# Ansible Basic Commands

## configure hosts

```bash
# edit hosts file
vi /etc/ansible/hosts

# or create inventory.ini
vi inventory.ini
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

# if you need a password
ansible 172.18.0.4 -a "echo test" --extra-vars "ansible_user=root ansible_password=p@ssword123"

```

## Execute Methods
- Ad-hoc execute
- Playbook execute

### Ad-hoc execute

```bash
# ping
ansible 172.18.0.4 -m ping

# test msg
ansible 172.18.0.4 -a "echo test"

# reboot
ansible 172.18.0.4 -a "mkdir testomato"
```

### Playbook execute

```bash
mkdir ansible-playbook
vi site.yml
```

```bash
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
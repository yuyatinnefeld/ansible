# Apache02 Webiste

1. create files folder and website files
```bash
mkdir files
cd files

# create an index.html file
vi index.html

# create a apache2.conf file
```

2. create a playbook file "site.yml"

```yaml
- hosts: 172.18.0.6
  tasks:
    - name: " 💪 install apache for Debian style 💪 "
      apt:
        name: apache2
        state: latest
      when: ansible_os_family == "Debian"

    - name: " 💪 install packages for RedHat style 💪 "
      yum:
        name: httpd
        state: latest
      when: ansible_os_family == "RedHat"


    - name: " ✨ create an apache2 folder ✨ "
      ansible.builtin.file:
        path: /etc/apache2/
        state: directory
        mode: '0755'

    - name: " ✨ create a www folder ✨ "
      ansible.builtin.file:
        path: /var/www/
        state: directory
        mode: '0755'

    - name: " ✨ setup config file ✨ "
      copy:
        src: apache2.conf
        dest: /etc/apache2/apache2.conf

    - name: " ✨ configure the top page ✨ "
      copy:
        src: index.html
        dest: /var/www/index.html

    - name: " 🚀 enable the apache service 🚀 "
      ansible.builtin.service:
        name: httpd
        state: started
```
```bash
ssh target01
ls -la /etc/apache2
ls -la /var/www
```
open the host domain: 172.18.0.6

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
- hosts: target01
  tasks:
    - name: " 💪 install apache for Debian style 💪 "
      apt:
        name: apache2
        state: latest
      when: ansible_os_family == "Debian"

    - name: " ✨ setup config file ✨ "
      copy:
        src: apache2.conf
        dest: /etc/apache2/apache2.conf

    - name: " ✨ configure the top page ✨ "
      copy:
        src: index.html
        dest: /var/www/index.hthtml

    - name: " 🚀 enable the apache service 🚀 "
      service:
        name: apache2
        state: started
        enabled: yes
      tags:
        - started
```

3. run playbook
```bash
cd apache_server
apache-playbook site.yml
```

4. visit the website
http://172.18.0.6/
create_user
=========

create a user and upload an ssh public key for remote authentication

Requirements
------------

No specifi requirement Ansible
Need a default ssh public key or a specific key needs to be called out in a variable.

Role Variables
--------------

# Define the user you would like to create
user_name: default

# Define the user state present or absent
user_state: present

# Define the path to the ssh public key
ssh_key: ~/.ssh/cloud_key.pub

Dependencies
------------

None

Example Playbook
----------------

- hosts: product_02
  tasks:
     - include_role:
         name: create_user
       vars:
         user_name: root
         ssh_key: ~/.ssh/cloud_key.pub


License
-------

MIT

BSD

Author Information
------------------

info@yuyatinnefeld.com

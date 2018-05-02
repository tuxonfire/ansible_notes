# 1. Ansible stuff
Ansible is...
Easer than others, ansible doesn't needs agent installed in servers.
ssh connection.
modules are building in python.
Modular and flexible.
Server management.
#  Ansible instalation
python 2.x
python 3 => ansible 2.4
user non root
generate a ssh private and public key (ssh-keygen) in order to create a trust relationship among server (private key) an nodes (public key)
put public key at .ssh/authorized_keys at home user nodes.
ssh-copy-id @ remotehost
EPEL repository.
(with Centos 7.4 EPEL repository is not necessary.)

# 1.3 inventary management
Remote hosts needs to be defined at inventary file.
Remote host are specified by their FQDN or IP adress.
-i <--- inventary host

Create a ansible directory, and put there a inventary file called inventario.
```
[ansible@saturn inventario]$ cat inventario
[all]
r2d2
deathstar
falconmilenario
[group1]
r2d2
[group2]
deathstar
```

```ansible r2d2 -i inventario --list-host```
  hosts (1):
    r2d2
```ansible all -i inventario --list-host ```
  hosts (3):
    falconmilenario
    deathstar
    r2d2


##### The ansible.cfg
The ansible.cfg is the configuration file of ansible, is located at /etc/ansible/ansible.cfg
This can be copied at project path as .ansible.cfg and be customized.
Also pre determinate module could be defined in ansible.cfg file.
With ansible --version you are able to see the version,path of ansible.cfg and stuff.
In order to scalate privileges, create a sudo file at hosts managed by ansible.
```
vi  /etc/sudoers.d/ansible
ansible ALL=(ALL) NOPASSWD: ALL
```
Privilege_scalation is important part of ansible.cfg file, thus grant privileges such as become root, and stuff like that.

This module allow eject commands in managed hosts

##### Modules
deep information with documentation modules:
```
 ansible-doc -l | grep ec2
 ansible-doc yum
```
Command: execute a command in a managed remote host. (Is important to know that shell can not interpret command module.)
shell execute a command through local shell.
copy: copy a file, change content in a remote host.

##### Command Module
Module  -m
A module is commonly used to do specifics tasks in ansible.
```
-m  module
-a  args

ansible all -m command -a env
ansible all -m ping
```
###### 1st lab
```
ansible [all or host managed] -m copy -a 'content="ANSIBLE FIRST LAB\n" dest=/etc/motd'

ansible -m command -a 'cat /etc/motd'
```
###### 2nd lab Module managed users documentation
```
ansible-doc -l | grep "user"
```
######  Creating YAML files.
Content
Dictionary: key:value
Lists: used to represent a list of elements.
The list are numbered as - item [with a space after - is mandatory]
cchain values can be used with "" or ''

Doing a syntax check in YAML file:
```
ansible-playbook --syntax-check myplaybook.yml
```
Running playbook install.yml
```
ansible-playbook install.yml
```
Installing Apache and Samba services, also starting services with Ansible.
```
---
- hosts: grupo1
  tasks:
    - name: Apache Install
      yum: name=httpd state=latest
    - name: Install Samba
      yum: name=samba state=latest
    - name: Starting Services
      service:
        name: "{{ item }}"
        enabled: true
        state: started
      with_items:
        - httpd
        - smb
...

```

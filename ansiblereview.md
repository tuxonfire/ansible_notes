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

#  Inventary management
Remote hosts needs to be defined at inventary file.
Remote host are specified by their FQDN or IP adress.
-i <--- inventary host

Create a ansible directory, and put there a inventary file called inventario.
```
[ansible@saturn inventario]$ cat inventario
[all]
r2d2
deathstar
milenaryfalcon
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
This can be copied at project path as .ansible.cfg and can be customized.
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
Dictionary: a dictionary is represented in a simple key: value form (the colon must be followed by a space)

Lists: used to represent a list of elements.
The list are numbered as - item [followed by space after - is mandatory]
chain values can be used with "" or ''

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
    - name: Firewall enable
      firewalld:
      service: https
      permanent: true
      immediate: true
      state: enabled
    - name: copiando contenido :v
      copy:
        content:"Welcome to my web"
        dest: /var/www/html/index.html
...

```
###### Playbooks execution:

playbooks are executed with arguments in order to check syntax:
```
 ansible-playbook --syntax-check myplaybook.yml
```
check mode without apply any change,also can be done with --check
```
 ansible-playbook -C myplaybook.yml
```
Running ansible playbook step by step
```
 ansible-playbook -step myplaybook.yml
```

ansible grupo1 -m yum -a 'name=httpd,samba state=absent'

###### Working with variables
Variables can be defined in playbook file:
vars.yml 
---
   db_package:mariadb-server
   db_service:mariadb
...

play_vars.yml

---
 - hosts: group2
   vars_files:
     - vars.yml
   tasks:
     - name: Instalar {{db_package}}  // variables are referenced using double brackets
       yum: 
         name: "{{ db_package }}" // if a variable is used as 1st element, is mandatory to use double quotation marks "{{ my_variable}}"
         state: latest
     - name: iniciando {{ db_service }}
       service: 
         name: "{{ db_service }}"
         state: started
         enabled: true
...



References
[Yaml Documentation](http://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html#yaml-basics)

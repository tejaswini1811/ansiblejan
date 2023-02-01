Nodejs
=========
## Manual Steps

* [Referhere](https://cloudcone.com/docs/article/how-to-install-node-js-on-ubuntu-22-04/) for ubuntu 22.04
* commands used:
  ```
   sudo apt update
   sudo apt install nodejs npm
  ```
* To check wheather nodejs is installed or not check version.
  ```
  node --version
  ```
  ![preview]()
* [Referhere](https://phoenixnap.com/kb/install-node-js-npm-centos) for centos7
* commands used:
   ```
   sudo yum update
   curl –sL https://rpm.nodesource.com/setup_10.x | sudo bash -
   sudo yum install –y nodejs
   ```
* To check wheather nodejs is installed or not check version.
  ```
  node --version
  ```
Ansible-playbook for both centos and ubuntu
-------------------------------------------
[Referhere](https://github.com/tejaswini1811/ansiblejan/blob/main/Ansible/ALLin1/nodejs.yaml)
## Creating roles:
* To ceate a role we have to following steps:
1. In local host we have to create a role using  " ansible-galaxy role init < rolename >" 
2. After creating role download the role files into your laptop floder using sftp.
  ```
  cd /path/
  sftp <username>@publicip
  sftp> get -r <rolename>
  ```
3. Add the roles floder to your git repository.
   
## After creating role:
* By using following commad you can get the roles directory into your machine.
```
git clone https://github.com/tejaswini1811/roles.git
```
Ansible-playbook:
```
---
- name: install nodejs on ubuntu and centos
  hosts: all
  become: yes
  roles:
    - <rolename>
```

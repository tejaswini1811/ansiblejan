# Tomcat installation on ubuntu 22.04


* For manual steps [referhere](https://linuxize.com/post/how-to-install-tomcat-10-on-ubuntu-22-04/)
  
Manual Steps:
-------------
  ```
  sudo apt update
  sudo apt install openjdk-11-jdk -y
  java -version
  sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
  VERSION=10.1.5
  wget https://www-eu.apache.org/dist/tomcat/tomcat-10/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz -P /tmp
  sudo tar -xf /tmp/apache-tomcat-${VERSION}.tar.gz -C /opt/tomcat/
  sudo ln -s /opt/tomcat/apache-tomcat-${VERSION} /opt/tomcat/latest
  sudo chown -R tomcat: /opt/tomcat
  sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
  sudo nano /etc/systemd/system/tomcat.service
  sudo systemctl daemon-reload
  sudo systemctl enable --now tomcat
  sudo systemctl status tomcat
  ``` 
* After doing those manual steps you get a tomcat page by entering <publicip>:8080
 ![preview](images/tomcat1.png)
 
Ansible-playbook for above commands:
------------------------------------
[referhere](https://github.com/tejaswini1811/ansiblejan/blob/main/Ansible/tomcat/tomcat.yml) for playbook.
* To ensure syntax is correct `ansible-playbook -i hosts --syntax-check tomcat.yml`
* To excute the playbook `ansible-playbook -i hosts tomcat.yml`
* After that you get check the status in node and check the tomcat page.
![preview](images/tomcat2.png)
Configuring Tomcat Web Management Interface
-------------------------------------------
* [referhere](https://linuxize.com/post/how-to-install-tomcat-10-on-ubuntu-22-04/#configuring-tomcat-web-management-interface) for manual steps of configuring tomcat web management.
* After doing those steps we access manager app and host manager buttons
![preview](images/tomcat3.png)
![preview](images/tomcat5.png)
* For ansible-playbook.
  ```
  ---
- name: tomcat on ubuntu
  hosts: all
  become: yes
  vars:
    java_version: openjdk-11-jdk
    user_home: /opt/tomcat
    user_shell: /bin/false
    tomcat_version: "10.1.5"
    tomcat_major_version: "10"
    user_name: tomcat
    group_name: tomcat
    tomcat_service_name: tomcat.service
  tasks:
    - name: installing java
      ansible.builtin.apt:
        name: "{{ java_version }}"
        update_cache: yes
        state: present
    - name: creating a system user
      ansible.builtin.user:
        name: "{{ user_name }}"
        create_home: true
        home: "{{ user_home }}"
        shell: "{{ user_shell }}"
        state: present
    - name: creating tomcat group
      ansible.builtin.group:
        name: "{{ group_name }}"
        state: present
    - name: downloading tomcat
      ansible.builtin.get_url:
        url: "https://www-eu.apache.org/dist/tomcat/tomcat-{{ tomcat_major_version }}/v{{ tomcat_version }}/bin/apache-tomcat-{{ tomcat_version }}.tar.gz" 
        dest: /tmp/
    - name: unarchive tar file
      ansible.builtin.unarchive:
        src: "/tmp/apache-tomcat-{{tomcat_version}}.tar.gz"
        dest: "{{ user_home }}"
        mode: '777'
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        remote_src: yes
    - name: creating symbolic link
      ansible.builtin.file: 
        src: "{{ user_home }}/apache-tomcat-{{tomcat_version}}"
        dest: "{{ user_home }}/latest"
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        state: link
        mode: '777'
    - name: tomcat's bin shell directory executable order
      ansible.builtin.command: "sudo  sh -c 'chmod +x {{ user_home }}/latest/bin/*.sh'"
    - name: creating tomcat.service
      ansible.builtin.copy:
        src: "{{ tomcat_service_name }}"
        dest: "/etc/systemd/system/{{ tomcat_service_name }}"
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        mode: '777'
    - name: enable and start tomcat service
      ansible.builtin.systemd: 
        name: "{{ tomcat_service_name }}"
        daemon_reload: yes
        enabled: yes
        state: started
    - name: copying tomcat-users.xml
      ansible.builtin.copy:
        src: tomcat-users.xml
        dest: "{{ user_home }}/latest/conf/tomcat-users.xml"
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        mode: '777'
    - name: copying context.xml
      ansible.builtin.copy:
        src: context.xml
        dest: "{{ user_home }}/latest/webapps/manager/META-INF/context.xml"
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        mode: '777'
    - name: copying hostmanager-context.xml
      ansible.builtin.copy:
        src: hostmanager-context.xml
        dest: "{{ user_home }}/latest/webapps/host-manager/META-INF/context.xml"
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        mode: '777'
    - name: restart tomcat service
      ansible.builtin.systemd:
        name: "{{ tomcat_service_name }}"
        state: restarted


  ```
* For tomacat- userxml. [Referhere]()
* After applying playbook we can access tomcat web.
![preview](images/tomcat4.png)
![preview](images/tomcat6.png)
* Added variables for ansible-playbook [referhere](https://github.com/tejaswini1811/ansiblejan/blob/main/Ansible/tomcat/tomcat.yml)

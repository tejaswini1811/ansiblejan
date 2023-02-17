# ansible
## Variables:
* [Refer Here](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#:~:text=Ansible%20uses%20variables%20to%20manage,syntax%2C%20including%20lists%20and%20dictionaries.) for variables in ansible.
* Variables are used to define arguments.
## Stat Module:
*  We need to skip certain steps to execute again because it was done in first time.
* By using ansible.builtin.stat module we can do that.
* [Refer Here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html#examples) for stat module. 
## Handlers:
* We can improve our playbook further by using handlers.
* [Referhere](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_handlers.html#handlers) for how to use handlers.
* Sometimes you want a task to run only when a change is made on a machine. For example, you may want to restart a service if a task updates the configuration of that service, but not if the configuration is unchanged. Ansible uses handlers to address this use case. Handlers are tasks that only run when notified.
  
## Loops:
* [Referhere](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html) for reference how to use loops in playbook.
* Ansible offers the loop, `with_<lookup>`, and until keywords to execute a task multiple times. Examples of commonly-used loops include changing ownership on several files and/or directories with the file module, creating multiple users with the user module, and repeating a polling step until a certain result is reached.
* It's like for loop in ansible.
* To do same task multiple times we use loops.
## Inventory File:
* We can write our inventory [hosts] in two formats.
     1. ini
     2. yaml
1. ### $ini:$ 
   * example of tomcat.
   ```ini
   [appservers]
   172.31.40.51 java_version=java-11-openjdk-devel
   172.31.40.226 java_version=openjdk-11-jdk

   [appservers:vars]
    user_home=/opt/tomcat
    user_shell=/bin/false
    tomcat_version= "10.1.5"
    tomcat_major_version= "10"
    user_name=tomcat
    group_name= tomcat
    tomcat_service_name= tomcat.service
   ```
2. ### $yaml:$ 
   * example of tomcat.
   ```yaml
   ---
   all:
     children: 
       appservers:
         hosts:
           172.31.40.51: 
             java_version: java-11-openjdk-devel
           172.31.40.226:
             java_version: openjdk-11-jdk
         vars: 
           user_home: /opt/tomcat
           user_shell: /bin/false
           tomcat_version: "10.1.5"
           tomcat_major_version: "10"
           user_name: tomcat
           group_name: tomcat
           tomcat_service_name: tomcat.service
   ```
* We can further seperate variables into `group_vars` and `host_vars`. 
* In `group_vars` we create a file with the name of hosts and declare the group variables.
* Same as `group_vars` in `host_vars` we create a individual files with hosts ip addresses and declare hosts individual variables.
## Template Module:
* Ansible uses jinja2 template 
* jinja2 is used in python.
* In ansible jinja2 can be used in files.
* By using template module in ansible we can provide variables in files by providing `.j2` extention. Example `tomcat-users.xml.j2` . For more dtails about template module [Refer Here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html).
## Find Module:
* Find module is used to find files with extentions.
* [Refer Here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/find_module.html) for find module usage.
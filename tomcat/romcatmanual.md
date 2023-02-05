# Tomcat manual steps from playbook

## Install jdk packages
* sudo apt update
* sudo apt install openjdk-11-jdk
## create tomcat group:
* sudo groupadd tomcat
## create user tomcat:
* sudo useradd -U tomcat -m -d /opt/tomcat -s /bin/false tomcat
## extract tomcat:
* VERSION= 10.1.5
```
 wget https://www-eu.apache.org/dist/tomcat/tomcat-10/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz -P /tmp -P /tmp/
```
* sudo tar /tmp/apache-tomcat-${VERSION}.tar.gz -o /opt/tomcat
## creating symbolic link:
* sudo ln -s /opt/tomcat/apache-tomcat-${VERSION} /opt/tomcat/latest
## change permissions to /opt/tomcat:
* sudo chown -R /opt/tomcat tomcat:tomcat
## add execute permissions to shell files:
* sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
## Create a systemd unit file:
* In /etc/systemd/system/tomcat.service insert data as below.
```
[Unit]
Description=Tomcat 10 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/opt/tomcat/latest"
Environment="CATALINA_HOME=/opt/tomcat/latest"
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/latest/bin/startup.sh
ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```
## reload daemon and start tomcat:
* sudo systemctl daemon-reload tomcat.service
## change the /opt/tomcat/latest/conf/tomcat-users.xml:
* add user and password in /opt/tomcat/latest/conf/tomcat-users.xml.
```
    <role rolename="admin-gui"/>
    <role rolename="manager-gui"/>
    <user username="admin" password="admin_password" roles="admin-gui,manager-gui"/>
</tomcat-users>
```
## edit context.xml:
* In  /opt/tomcat/latest/webapps/manager/META-INF/context.xml and /opt/tomcat/latest/webapps/host-manager/META-INF/context.xml.
```
<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow=".*" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```
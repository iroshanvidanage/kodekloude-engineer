## Install and Configure Tomcat Server

### Task

The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:


a. Install tomcat server on App Server 2 using yum.

b. Configure it to run on port 6000.

c. There is a ROOT.war file on Jump host at location /tmp. Deploy it on this tomcat server and make sure the webpage works directly on base URL.

---

### Solution

1. Create the user and usergroup for Tomcat server

```sh

# stapp02
# switch to root user
sudo -i

# verify java is installed
java -version

# if not install java
yum install java-11-openjdk-devel -y
java -version

# create user for tomcat nologin
groupadd tomcat
mkdir -p /usr/local/tomcat
useradd -r -g tomcat -d /usr/local/tomcat -s /bin/nologin tomcat

```

---

2. Install and configure Tomcat server

```sh

# download tomcat server
cd /tmp
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.108/bin/apache-tomcat-9.0.108.tar.gz
tar -xvzf apache-tomcat-9.0.108.tar.gz -C /usr/local/tomcat --strip-components=1

# set dir ownership and permissions
chown -R tomcat: /usr/local/tomcat
chmod -R 755 /usr/local/tomcat
```

- Create the systemd file in the *location:*`/etc/systemd/system/tomcat.service` using the following

```sh
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.20.1.1-2.el9.x86_64"
Environment="CATALINA_PID=/usr/local/tomcat/temp/tomcat.pid"
Environment="CATALINA_HOME=/usr/local/tomcat"
Environment="CATALINA_BASE=/usr/local/tomcat"
ExecStart=/usr/local/tomcat/bin/startup.sh
ExecStop=/usr/local/tomcat/bin/shutdown.sh
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

---

3. Reload Systemd and Start Tomcat: Reload the systemd daemon to recognize the new service and then start and check the status of the Tomcat service.

```sh
sudo systemctl daemon-reload
sudo systemctl start tomcat.service
sudo systemctl status tomcat.service
```

---

4. Copy the ROOT.war

*Initial location:*`jumphost:/tmp/ROOT.war`

```sh

# login to remote server:stapp02 using sftp protocol
sftp steve@stapp02

# change to webapps dir
cd /usr/local/tomcat/webapps/

# upload the file to the remote server
put /tmp/ROOT.war .

```

---

5. Start the Tomcat server

- Change the port in Tomcat to `6000` in the *location:*`/usr/local/tomcat/conf/server.xml`

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
```

- Restart the Tomcat server

```sh
systemctl stop tomcat.service
systemctl start tomcat.service

# can use the restart command as well.

systemctl status tomcat.service
```

---

6. If a firewall is active open the port 6000 to allow access.

```sh
firewall-cmd --permanent --add-port=6000/tcp
firewall-cmd --reload
```

---

7. Check the connectivity

- Check the conneectivity from the jump host using following command.

`curl http://stapp02:6000`
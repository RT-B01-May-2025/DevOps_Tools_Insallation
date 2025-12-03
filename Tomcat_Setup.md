## Java 11 & Tomcat 9 Installation Guide on Amazon EC2 (Amazon Linux 2) & RHEL 8/9 & Ubuntu

Step-by-step instructions to install **Java 11** and **Apache Tomcat 9** on **Amazon Linux 2** and **Red Hat Enterprise Linux (RHEL 8/9)** and **Ubuntu** servers.

---

## Install Java 11

### 🔹 Amazon Linux 2

```bash
sudo yum update -y
sudo yum install -y java-11-amazon-corretto
java -version
```

### 🔹 Ubuntu

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
java -version
```


### 🔹 RHEL 8/9

```bash
cd /opt
sudo  wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
sudo tar -xzf openjdk-11.0.2_linux-x64_bin.tar.gz
sudo mv jdk-11.0.2 java-11
sudo ln -s /opt/java-11/bin/java /usr/bin/java
echo 'export JAVA_HOME=/opt/java-11' | sudo tee -a /etc/profile
echo 'export PATH=$PATH:$JAVA_HOME/bin' | sudo tee -a /etc/profile
source /etc/profile

java -version
```

---

## Install Apache Tomcat 9 (Manual)

### Common Steps for Amazon Linux 2 & RHEL & Ubuntu

```bash
# Download and extract Tomcat 9
cd /opt
sudo yum install wget -y
# In case of Ubuntu/Debiain
#sudo apt install wget -y
sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.107/bin/apache-tomcat-9.0.107.tar.gz
sudo tar -xvzf apache-tomcat-9.0.107.tar.gz
sudo mv apache-tomcat-9.0.107 tomcat
sudo rm apache-tomcat-9.0.107.tar.gz

# Set permissions
sudo chown -R ec2-user:ec2-user /opt/tomcat
# In Case Of Ubuntu
# sudo chown -R ubuntu:ubuntu /opt/tomcat
sudo chmod +x /opt/tomcat/bin/startup.sh
sudo chmod +x /opt/tomcat/bin/shutdown.sh
sudo chmod +x /opt/tomcat/bin/catalina.sh
```

---

## Starting & Stopping Tomcat
- Navigate to the bin directory within your Tomcat installation.
- Execute the appropriate startup script for your operating system:
- For Unix-based systems (Linux, macOS): startup.sh

### This will start the Tomcat server.

```bash
cd /opt/tomcat/bin
sudo ./startup.sh
```

### This will stop the Tomcat server.

```bash
cd /opt/tomcat/bin
sudo ./shutdown.sh
```

### Usage of Catalin.sh

* Location: $CATALINA_HOME/bin/catalina.sh
* Role: Core script that controls all Tomcat lifecycle commands.
* More flexible: Accepts multiple command-line options.

>Used for:

- Starting (catalina.sh start)
- Stopping (catalina.sh stop)
- Debugging (catalina.sh jpda start)
- Running in the foreground (catalina.sh run)
- Config testing, version info (catalina.sh version)

```bash
./catalina.sh start
./catalina.sh stop
./catalina.sh run
```
---
## (Optional) Create Systemd Service for Tomcat

```bash
sudo tee /etc/systemd/system/tomcat.service > /dev/null <<EOF
[Unit]
Description=Apache Tomcat 9 Web Application Container
After=network.target

[Service]
Type=forking

User=ec2-user
Group=ec2-user

Environment="JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_BASE=/opt/tomcat"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

---

## (Optional) Start and Enable Tomcat

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat
```

---

## Access Tomcat Web Interface

Open your browser and go to:

```
http://<YOUR_EC2_PUBLIC_IP>:8080
```

---

## (Optional) Enable Remote Access to Manager/Host Manager

Edit:

- `/opt/tomcat/webapps/manager/META-INF/context.xml`
- `/opt/tomcat/webapps/host-manager/META-INF/context.xml`

Comment the IP restriction block:

```xml
<!--
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
       allow="127\.\d+\.\d+\.\d+|::1" />
-->
```

---

## Notes

- Default Tomcat ports: `8080`, `8009`, `8443`
- Default Tomcat webapps: `/opt/tomcat/webapps`
- To deploy `.war` files: Place them under `/opt/tomcat/webapps`


# SonarQube 25.6.0 Setup on Linux

## Prerequisites

| Requirement        | Details                          |
|--------------------|----------------------------------|
| Java               | Java 17+ (OpenJDK/Temurin)       |
| Database           | PostgreSQL 12+ recommended       |
| RAM                | 2 GB minimum, 8 GB recommended    |
| OS                 | Linux (Ubuntu, RHEL, etc.)       |
| Privileges         | Non-root user for SonarQube      |

---

## Download Java 17 Or Above For SonarQube 25.x 

```bash
cd /opt
sudo  wget https://download.java.net/openjdk/jdk17.0.0.1/ri/openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo tar -xvzf openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo rm openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo mv jdk-17.0.0.1  java-17
sudo ln -s /opt/java-17/bin/java /usr/bin/java
echo 'export JAVA_HOME=/opt/java-17' | sudo tee -a /etc/profile
echo 'export PATH=$PATH:$JAVA_HOME/bin' | sudo tee -a /etc/profile
source /etc/profile

java -version

```

## Download SonarQube 25.6.0

```bash
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.6.0.109173.zip
unzip sonarqube-25.6.0.109173.zip
sudo mv sonarqube-25.6.0.109173 /opt/sonarqube
```

---


## Create and Assign Sonar User

```bash
sudo useradd -r -M sonar
sudo chown -R sonar:sonar /opt/sonarqube
```

<!-- --- Ignore this piece of code
### Note: Make sure always start sonarqube as sonar user don't try to start as any other user. If SonarQube is failed to start delete /opt/sonarqube/temp folder then start sonarqube as sonar user.
---
## Start SonarQube


```bash
sudo -u sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh start
```

Check logs:

```bash
tail -f /opt/sonarqube/logs/sonar.log
```

Ignore till here --- --> 

## Create a Systemd Service

```bash
sudo vi /etc/systemd/system/sonarqube.service
```

Add:

```ini
[Unit]
Description=SonarQube 25.6.0
After=network.target

[Service]
Type=forking
User=sonar
Group=sonar
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
LimitNOFILE=65536
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable sonarqube
sudo systemctl start sonarqube
sudo systemctl status sonarqube
```
---

## 🌐 Step 9: Access SonarQube

URL: `http://<your-ip>:9000`  
Default login: `admin` / `admin`

---

## Optional (Recommended for Real Projects): External DB PostgreSQL Setup


## Install PostgreSQL

```bash
sudo apt update
sudo apt install  postgresql -y
```

---

## Configure PostgreSQL

```bash
sudo -u postgres psql
```

```sql
CREATE DATABASE sonarqube;
CREATE USER sonar WITH ENCRYPTED PASSWORD 'ChangeMe123';
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
\q
```

---

## Configure SonarQube To Use PostgreSQL

Edit the config file:

```bash
sudo nano /opt/sonarqube/conf/sonar.properties
```

Update JDBC settings:

```ini
sonar.jdbc.username=sonar
sonar.jdbc.password=ChangeMe123
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
```
---

## (Optional) JVM Memory Configuration for SonarQube 25.x

### Locate the Wrapper Configuration
SonarQube 25.x no longer uses wrapper.conf for JVM options.

Instead, JVM settings are now configured in the conf/sonar.properties or via environment variables in the bin/linux-x86-64/sonar.sh script.

```bash
vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
```
Look for or add the following block near the top of the file:

```bash
# JVM memory options
export SONAR_JAVA_OPTS="-Xms1g -Xmx2g -XX:+UseG1GC -Dfile.encoding=UTF-8"
```
Apply and Restart
Restart SonarQube to apply changes:
```bash
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh restart
```

### Best Practice for Production
- For small environments, use -Xms512m -Xmx1g
- For medium setups, use -Xms2g -Xmx4g
- For large-scale enterprise, monitor memory usage with tools and tune accordingly

---

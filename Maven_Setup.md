

This guide walks you through installing **Apache Maven 3.9.10** on a **Amazon Linux EC2 instance**, including Java installation, Maven setup, and environment configuration.

---

## 🛠 System Requirements

- **JDK**: 11 or above (Java is required before installing Maven)
- **Java 17** Preffered but depends on Application 
- **OS**: Any Linux flavor; Tested on Amzon Linux EC2
- **Maven Version**: 3.9.10

> All external software is installed in the `/opt` directory.

---

## 📋 Prerequisites

- Must be **logged in as root user** or use `sudo`
- Internet connection required (to download Maven and Java)

---

## 📦 Step-by-Step Installation Instructions(Works On Any Linux Distribution)

### ✅ Step 1: Switch to Root User

```bash
sudo su -
```

---

### ✅ Step 2: Install Java 17 (JDK 17)

You can install **Java 11**  or **Above**

### We Can Find Java Softwares Open JDK Versions Here:-  https://jdk.java.net/archive/


```bash
# For JDK 17
yum install wget -y

# In case of ubuntu or debian O.S
# apt install wget -y

cd /opt
sudo  wget https://download.java.net/openjdk/jdk17.0.0.1/ri/openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo tar -xvzf openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo rm openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo mv jdk-17.0.0.1  java-17
sudo ln -s /opt/java-17/bin/java /usr/bin/java

# User Level (Exit as a Root User and Run as Regular/Normal User (Only It will work for current User)
echo 'export JAVA_HOME=/opt/java-17' >> ~/.bash_profile
echo 'export PATH=$PATH:$JAVA_HOME/bin' >> ~/.bash_profile
source ~/.bash_profile

# Sytem Level For All Users
sudo tee /etc/profile.d/java.sh <<EOF
export JAVA_HOME=/opt/java-17
export PATH=\$JAVA_HOME/bin:\$PATH
EOF
source /etc/profile.d/java.sh

```

✅ Check the version:

```bash
java -version
javac -version
```
---

### ✅ Step 3: Prepare Environment

Install required tools and go to the `/opt` directory:

```bash
cd /opt
sudo su -
yum install -y wget unzip

# In case of ubuntu or debian O.S
# apt install wget unzip -y
```
---

### ✅ Step 4: Download and Extract Maven

```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.zip
unzip apache-maven-3.9.10-bin.zip
rm apache-maven-3.9.10-bin.zip # Remove Once it's Unzipped.
```

---

### ✅ Step 5: Set Environment Variables

Instead of updating user-specific profiles, we’ll set up a global script:

```bash

# User Level (Exit as a Root User and Run as Regular/Normal User (Only It will work for current User)

echo 'export M2_HOME=/opt/apache-maven-3.9.10' >> ~/.bash_profile
echo 'export PATH=$PATH:$M2_HOME/bin' >> ~/.bash_profile

source ~/.bash_profile

# Sytem Level For All Users

sudo tee /etc/profile.d/mvn.sh <<EOF
export M2_HOME=/opt/apache-maven-3.9.10
export PATH=\$PATH:\$M2_HOME/bin
EOF

source /etc/profile.d/mvn.sh

---

### ✅ Step 6: Verify Maven Installation

```bash
mvn -version
```

Expected output:

```
Apache Maven 3.9.10 (...)
Java version: 11.x or 17.x
```

---

## ⚠️ Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `mvn: command not found` | Environment not loaded | Run `source /etc/profile.d/mvn.sh` |
| `java: command not found` | Java not installed or  environment not loaded| Install Java Or Run `source /etc/profile.d/javav.sh`|
| Permission denied | Not root user | Use `sudo` or switch to root |

---

## Using Package Managers

### Note: If you want to install specific versions of java and maven, then install manually by download specific versions of java & maven tar/zip files separately then install and set JAVA_HOME, M2_HOME & Add Java & Maven bin to PATH

### Amazon Linux 2

Check Already Installed Java Packages using yum

```bash
java -version
yum list installed | grep java
```

Example output

```bash
java-11-openjdk.x86_64
java-11-openjdk-devel.x86_64
```
If You have to Uninstall Java Packages Installed Using yum 

```bash
sudo yum remove java-11-openjdk java-11-openjdk-devel
# If you want to remoove all
sudo yum remove java-*
# Verify after removing 
java -version
```

## Install Maven With Java Using yum
```bash
sudo yum update -y
# Maven Depends On Java So Maven With Java Also will be installed.
sudo yum install maven -y
java -version
javac -version
mvn --version
```

### Ubuntu (20.04 / 22.04 / 24.04)


Check Already Installed Java Packages using apt

```bash
java -version
dpkg --list | grep -i java
```

Example output

```bash
openjdk-17-jdk
openjdk-11-jdk
```
If You have to Uninstall Java Packages Installed Using apt 

```bash
sudo apt remove openjdk-17-jdk
# This will Remove Configuration file also 
sudo apt purge openjdk-17-jdk
# After removal, clean up unnecessary/unused dependencies:
sudo apt autoremove
# Verify after removing 
java -version
```

## Install Maven With Java Using apt

```bash
sudo apt update
# Maven Depends On Java So Maven With Java Also will be installed.
sudo apt install maven -y
java -version
javac -version
mvn --version
```

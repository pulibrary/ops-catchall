# Don't Panic

## Create a VM
  * 32GB RAM
  * 8 CPUS
  * Register (when needed) DNS for VM and loadbalancer
  * 90 GB Drive

## Setup Pre-requisites

### Java

Check for version of Java (we want 11)
 ```bash
 apt-cache search OpenJDK | grep 11
```
Install the results of that with:
```bash
sudo apt -y install openjdk-11-jdk
```
### Apache-Solr

Create a new directory to save your file `mkdir solr` cd into it. Download the and install solr following the [Go to Production](https://solr.apache.org/guide/solr/latest/deployment-guide/taking-solr-to-production.html) steps:
Ensure solr is listening on all interfaces because Archivesspace fails to run
```bash
wget https://archive.apache.org/dist/solr/solr/9.6.1/solr-9.6.1.tgz
tar xzf solr-9.6.1.tgz solr-9.6.1/bin/install_solr_service.sh --strip-components=2
sudo bash ./install_solr_service.sh solr-9.6.1.tgz
sudo service solr status
```

### MySQL

```bash
sudo apt -y update
sudo apt -y install mysql-server-8.0
sudo mysql_secure_installation
```

### Archivesspace

Download and install the Archivesspace
```bash
mkdir archivesspace
cd archivesspace
wget https://github.com/archivesspace/archivesspace/releases/download/v3.5.1/archivesspace-v3.5.1.zip
unzip archivesspace-v3.5.1.zip
sudo mv archivesspace /opt
cd /opt/archivesspace
./archivesspace.sh
```
edit the archivesspace config file to allow it to use the pre-requisites configuration above
```bash
cd /opt/archivesspace/lib
curl -Oq https://repo1.maven.org/maven2/com/mysql/mysql-connector-j/8.0.33/mysql-connector-j-8.0.33.jar
mysql -uroot -p
create database archivesspace default character set utf8mb4;
create user 'as'@'localhost' identified by 'as123';
```

edit `/opt/archivesspace/config/config.rb` with the values above

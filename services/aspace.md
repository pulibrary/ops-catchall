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

We will not be installing SolrCloud which Archivesspace does not support. Create a solr user with the following.
```bash
sudo useradd -m -U -d /opt/solr solr
```
Give the solr user a higher ulimit by editing the ulimit config with the following:

`sudo vim /etc/security/limits.d/solr.conf`

and add 

```file
solr   soft    nofile  65536
solr   hard    nofile  65536
```

Download and Install Apache Solr
```bash
mkdir ~/solr
wget [https://archive.apache.org/dist/solr/solr/8.11.3/solr-8.11.3.tgz](https://archive.apache.org/dist/lucene/solr/8.11.3/solr-8.11.3.tgz)
sudo tar xzf solr-8.11.3.tgz -C ~/solr
sudo rm -rf /opt/solr
sudo mv ~/solr/solr-8.11.3
sudo ln -sf /opt/solr-8.11.3 /opt/solr
sudo chown -R solr:solr /opt/solr
```
Create the SystemD unit file for solr with the following:

`sudo vim /etc/systemd/system/solr.service`

```file
[Unit]
Description=Apache Solr
After=network.target

[Service]
Type=forking
User=solr
Group=solr
Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
WorkingDirectory=/opt/solr
ExecStart=/opt/solr/bin/solr start -f
ExecStop=/opt/solr/bin/solr stop -all
TimeoutStartSec=180 
TimeoutStopSec=180

[Install]
WantedBy=multi-user.target
```
Enable the new service with the following:

```bash
sudo systemctl daemon-reload
sudo systemctl start solr.service
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

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
wget [[https://archive.apache.org/dist/solr/solr/8.11.3/solr-8.11.3.tgz](https://downloads.apache.org/lucene/solr/8.11.2/solr-8.11.2.tgz)](https://downloads.apache.org/lucene/solr/8.11.2/solr-8.11.2.tgz
sudo tar xzf solr-8.11.3.tgz
sudo bash solr-8.11.3/bin/install_solr_service.sh solr-8.11.3.tgz
```
The resulting SystemD unit file for solr fails. We will replace it with the following:

`sudo vim /etc/systemd/system/solr.service`

```file
[Unit]
Description=Apache Solr
After=network.target

[Service]
Type=simple
User=solr
Group=solr
ExecStart=/opt/solr/bin/solr start -f
ExecStop=/opt/solr/bin/solr stop
LimitNOFILE=1048576
TimeoutStartSec=300
Restart=on-failure

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
sudo useradd -m -U -d /opt/archivesspace archivesspace
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
```
Create the Archivesspace database and user with the following:

```bash
sudo su
mysql
```
Create the database and user with

```sql
create database archivesspace default character set utf8mb4;
create user 'as'@'localhost' identified by 'as123';
select user, authentication_string, plugin, host from mysql.user where user = 'as';
grant all privileges on archivesspace.* to 'as'@'localhost' with grant option;
```
edit `/opt/archivesspace/config/config.rb` with the values above

Copy the solr configuration files for archivesspace with the following commands:

```bash
sudo su - solr -c "/opt/solr/bin/solr create -c archivesspace"
sudo cp /opt/archivesspace/solr/*.txt /var/solr/data/archivesspace/conf/
sudo cp /opt/archivesspace/solr/schema.xml /var/solr/data/archivesspace/conf/
sudo chown -R solr:solr /var/solr/data/archivesspace/conf/
sudo systemctl restart solr
```

Test the Archivesspace installation with the demo DB doing the following:

```bash
cd /opt/archivesspace
./archivesspace.sh
```

If this works create an archivesspace init.d file with the following:

```bash
scripts/setup-database.sh 
cd /etc/init.d
sudo ln -s /opt/archivesspace/archivesspace.sh archivesspace
sudo ./archivesspace start
```

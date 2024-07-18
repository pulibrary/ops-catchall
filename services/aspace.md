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

```bash
wget https://archive.apache.org/dist/solr/solr/9.6.1/solr-9.6.1.tgz
tar xzf solr-9.6.1.tgz solr-9.6.1/bin/install_solr_service.sh --strip-components=2
sudo bash ./install_solr_service.sh solr-9.6.1.tgz
sudo service solr status
```

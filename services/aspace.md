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

Create a new directory to save your file `mkdir solr` cd into the 

Create a solr user

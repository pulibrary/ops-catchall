## Icinga2 installation

The following components are needed for our Icinga2 implementation

  * Icinga2
    * Icingadb (redis)
    * Icinga DB Daemon (PostgreSQL)
  * [IcingaWeb](icinga2_web.md)
  * Icinga Director
  * Icinga Certificate

### Icinga2

The project [documentation](https://icinga.com/docs/icinga-2/latest/doc/02-installation/02-Ubuntu/) will be our guide

 1. As `root` Add the Icinga Ubuntu repository with the following:
    ```bash
    sudo bash

    apt -y update

    apt -y install apt-transport-https wget gnupg

    wget -O - https://packages.icinga.com/icinga.key | gpg --dearmor -o /usr/share/keyrings/icinga-archive-keyring.gpg

    . /etc/os-release; if [ ! -z ${UBUNTU_CODENAME+x} ]; then DIST="${UBUNTU_CODENAME}"; else DIST="$(lsb_release -c| awk '{print $2}')"; fi; \
     echo "deb [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/ubuntu icinga-${DIST} main" > \
     /etc/apt/sources.list.d/${DIST}-icinga.list
     echo "deb-src [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/ubuntu icinga-${DIST} main" >> \
     /etc/apt/sources.list.d/${DIST}-icinga.list

    apt -y update
    ```
2. Install Icinga2 and Icinga Plugins
   This will install Icinga for both agents and master nodes. For agents installation this is the only step required. You will want to go back to [agent configuration](icinga2_agents.md)
   ```bash
   sudo apt -y install icinga2 monitoring-plugins
   ```
 3. Set Up Icinga2 API
    ```bash
    sudo icinga2 api setup
    ```
    The step above will auto generate a password in the configuration file at `/etc/icinga2/conf.d/api-users.conf` which we will need later. It will also enable the api feature for icinga2
    Restart Icinga2 to enable the API feature
    ```bash
    sudo systemctl restart icinga2
    ```
  4. Set Up IcingaDB (Redis)
     ```bash
     sudo apt -y install icingadb-redis
     ```
     Enable and start the db with the following:
     ```bash
     sudo systemctl enable --now icingadb-redis-server
     sudo systemctl restart icingadb-redis-server
     ```
     All the monitoring, configuration, check results, state changes, downtimes, and acknowledgement data are saved to this Redis server.

     Enable the Icinga db feature with the following steps:
     ```bash
     sudo icinga2 feature enable icingadb
     sudo systemctl restart icinga2
     ```

   5. Install and Set Up IcingaDB Daemon (PostgreSQL)
      ```bash
      sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'  
      wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc |    sudo apt-key add -  
      sudo apt-get update  
      sudo apt-get -y install postgresql postgresql-contrib icingadb
      ```
      Set up a PostgreSQL database for Icinga DB:
      ```bash
      sudo su -l postgres
      createuser -P icingadb # save password to lastpass
      createdb -E UTF8 --locale en_US.UTF-8 -T template0 -O icingadb icingadb
      psql icingadb <<<'CREATE EXTENSION IF NOT EXISTS citext;'
      ```
      Keeping in mind the postgresql reads the `pg_hba.conf` file in order edit `/etc/postgresql/15/main/pg_hba.conf` and add the following:

      ```conf
      local all icingadb scram-sha-256 
      host all icingadb 0.0.0.0/0 scram-sha-256 
      host all icingadb ::/0 scram-sha-256
      ```
      Reload your postgresql database with:

      ```bash
      sudo systemctl reload postgresql
      ```
      Import the Icinga DB schema using the following command:

      ```bash
      psql -U icingadb icingadb < /usr/share/icingadb/schema/pgsql/schema.sql
      ```

      Icinga DB installs its configuration file to `/etc/icingadb/config.yml`, pre-populating most of the settings for a local setup. Before running Icinga DB, adjust the Redis and database credentials and, if necessary, the connection configuration. Specifically make sure you switch the database from `mysql` to `pgsql` then add the password from earlier in step 5. 
      Enable icingadb with the following command:
      ```bash
      sudo systemctl enable --now icingadb
      sudo systemctl restart icingadb
      ````

   6. Set Up the monitoring database by installing the following:
      ```bash
      sudo apt -y install icinga2-ido-pgsql
      ```
      Use the password credentials you have used above at the prompts
      Examine the file at this location `/etc/dbconfig-common/icinga2-ido-pgsql.conf` and make changes
      ```bash
       sudo icinga2 feature enable ido-pgsql
       sudo systemctl restart icinga2
       ```

   7. You can proceed to [setup the IcingaWeb2](icinga2_web.md)

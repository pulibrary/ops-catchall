## Icinga installation

The following components are needed to install Icinga

  * Icinga2
    * Icingadb (redis)
    * Icinga DB Daemon (PostgreSQL)
  * IcingaWeb
  * Icinga Director
  * Icinga Certificate

### Icinga2

The project [documentation](https://icinga.com/docs/icinga-2/latest/doc/02-installation/02-Ubuntu/) will be our guide

 1. As `root` Add the Icinga Ubuntu repository with the following:
    ```bash
    sudo bash
    apt update
    apt -y install apt-transport-https wget gnupg
    wget -O - https://packages.icinga.com/icinga.key | gpg --dearmor -o /usr/share/keyrings/icinga-archive-keyring.gpg
    . /etc/os-release; if [ ! -z ${UBUNTU_CODENAME+x} ]; then DIST="${UBUNTU_CODENAME}"; else DIST="$(lsb_release -c| awk '{print $2}')"; fi; \
    echo "deb [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/ubuntu icinga-${DIST} main" > \
    /etc/apt/sources.list.d/${DIST}-icinga.list
    echo "deb-src [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/ubuntu icinga-${DIST} main" >> \
    /etc/apt/sources.list.d/${DIST}-icinga.list

    apt update
    ```
2. Install Icinga2 and Icinga Plugins
   ```bash
   sudo apt -y install icinga2 vim-icinga2 monitoring-plugins
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
      ```
    6. Set Up the monitoring database by installing the following:
      ```bash
      sudo apt -y install icinga2-ido-pgsql
      ```
      Use the password credentials you have used above at the prompts
      Examine the file at this location `/etc/dbconfig-common/icinga2-ido-pgsql.conf` and make changes

### IcingaWeb

The project [documentation](https://icinga.com/docs/icinga-web/latest/doc/02-Installation/02-Ubuntu/#install-the-web-server) will be our guide

  1. Install Icinga Web2 with the following:
     ```bash
     sudo apt-get install icingaweb2 libapache2-mod-php icingacli
     ```
     Prepare the Web Setup by creating the token we will pass to the web browser during setup. Run the following command:

     ```bash
     sudo icingacli setup token create
     ```

     Hint: If you need this later you can get the resulting token by running the following:
     ```bash
     sudo icingacli setup token show
     ```

     We will now need to create the fall back authentication by creating a database that will have the initial admin user. Create the database with the following:

     ```bash
     sudo su -l postgres 
     createuser -P icingaweb2 # save this to lastpass
     createdb -E UTF8 --locale en_US.UTF-8 -T template0 -O icingaweb2 icingaweb2
     ```

   2. Finally visit Icinga Web 2 in your browser to access the setup wizard and complete the installation:
      ```bash
      /icingaweb2/setup
      ```
      * Enter the token you created above in step 1 select *Next*
      * Toggle the *Migrate* and *Doc* buttons and leave the monitoring one on then select *Next*
      * Make sure all the modules are enabled and select *Next*
      * For authentication type select *Database* (LDAP will be configured later) and select *Next*
        * Leave Resource Name with the default Name
        * For *Database* choose PostgreSQL from the drop down
        * Enter the database credentials from step 1 the *Validate* and click *Next*
        * Leave the default backend name by selecting *Next*
      * Create the admin user icingaadmin:<secretpassword> # save to lastpass
      * For Application Configuration change *Syslog* to *File* and accept the defaults the click *Next* to complete configuration. Select *Next* and review the configuration. You can complete the configuration with *Next* if it looks OK. Or you can edit whatever looks off.
      * Set up the monitoring by selecting *PostgreSQL* from the drop down and enter the icingadb2 credentials from step 6 above
        * you may need to run `sudo icinga2 feature enable ido-pgsql`
      * Set up command transport by entering the credentials from `/etc/icinga2/conf.d/api-users.conf` *Validate* then click *Next*
      * Accept the defaults on *Monitoring Security* then Finish you Installation by Clicking *Finish*


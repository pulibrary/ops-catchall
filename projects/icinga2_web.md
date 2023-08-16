### IcingaWeb

The project [documentation](https://icinga.com/docs/icinga-web/latest/doc/02-Installation/02-Ubuntu/#install-the-web-server) will be our guide. These steps assume you already have [Icinga2](icinga.md) setup

  1. Install Icinga Web2 with the following:
     ```bash
     sudo apt -y install icingaweb2 libapache2-mod-php icingacli php8.1-imagick
     ```
     We will now need to create the fall back authentication by creating a database that will have the initial admin user. Create the database with the following:

     ```bash
     sudo su -l postgres 
     createuser -P icingaweb2 # save this to lastpass
     createdb -E UTF8 --locale en_US.UTF-8 -T template0 -O icingaweb2 icingaweb2
     ```
     Prepare the Web Setup by creating the token we will pass to the web browser during setup. Run the following command:

     ```bash
     sudo icingacli setup token create
     ```

     Hint: If you need this later you can get the resulting token by running the following:
     ```bash
     sudo icingacli setup token show
     ```


   2. Restart the Webserver and Icinga2 application then visit Icinga Web 2 in your browser to access the setup wizard and complete the installation:
      ```bash
      sudo systemctl restart apache2
      sudo systemctl restart icinga2
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

  3. The following steps will set up LDAP authentication
     * Open the file `/etc/icingaweb2/resources.ini` and add this block
       ```ini
       [pu_ldap]
        type = "ldap"
        hostname = "ldapproxy.princeton.edu"
        port = "636"
        encryption = "ldaps"
        root_dn = "dc=pu,dc=win,dc=princeton,dc=edu"
        bind_dn = "CN=<service account>,OU=Library - Office of the Deputy University Librarian,OU=People,DC=pu,DC=win,DC=princeton,DC=edu"
        bind_pw = "s3cr3tp455"
        timeout = "5"
       ```
     * Open the file `/etc/icingaweb2/authentication.ini` and add this block
       ```ini
       [ldap-user-auth]
        backend = "ldap"
        resource = "pu_ldap"
        user_class = "user"
        user_name_attribute = "sAMAccountName"
        domain = "princeton.edu"
        filter = "(&(objectCategory=Person)(sAMAccountName=*))"
        ```
     * Open the file `/etc/icingaweb2/groups.ini` and add this block
       ```ini
       [ldap-group-auth]
        backend = "ldap"
        resource = "pu_ldap"
        group_class = "group"
        group_name_attribute = "cn"
        group_filter = "objectCategory=Group"
        user_backend = "ldap-user-auth"
        group_member_attribute = "member" 
     * Open the file `/etc/icingaweb2/roles.ini` and add this block
       ```ini
        [Administrators]
         users = "icingaadmin, netid@PRINCETON.EDU"
         permissions = "*"
         groups = "Administrators"
        ```
   4. From the WebUI navigate to the icingaadmin and select Configuration --> Application
     * Select *Access Control Backends* Tab and make the *pu_ldap* the first authentication backend resource
   5. You can now [Setup Agents/Satellites](icinga2_agents.md)

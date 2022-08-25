## Installing Postgresql

Installing PostgreSQL from the PostgreSQL repository allows you more control over what version to choose. The following process installs the latest stable version of PostgreSQL. As of Fall 2022, this is version 15.0. You can also choose to install an earlier release of PostgreSQL.

  * Update and upgrade the existing packages.
  
  ```bash
  sudo apt update
  sudo apt -y upgrade
  ```

  * Add the new file repository configuration. 
  
  ```bash
  sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
  ```

  * Import the signing key for the repository.
  
  ```bash
  wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
  ```

  * Update the package lists.

 ```bash
 sudo apt-get update
 ```

  * Install the latest version of PostgreSQL.
  
```bash
sudo apt-get -y install postgresql postgresql-contrib
```

  * Ensure PostgreSQL is running with `systemctl`.
`
```bash
sudo systemctl start postgresql.service
```

  * To automatically launch PostgreSQL upon system boot-up, register it with `systemctl`.

```bash
sudo systemctl enable postgresql.service
```

### Accessing the PostgreSQL Shell

PostgreSQL automatically creates a default user named postgres upon installation. The `postgres` user has full superadmin privileges, Ordinarily you will need to to implement Linux and database passwords for the account but that is beyond the scope of this tutorial.

  * Switch over to the postgres account.
  
```bash
sudo su - postgres
```

  * Confirm PostgreSQL is working properly and you are running the version you expect with the following command. This command returns the version of the PostgreSQL server.

```bash
psql -c "SELECT version();"
```

This command returns the version of the server component, which might not be as recent as the overall release number.

PostgreSQL grants access to local system users without requiring a password. This is known as peer authentication. PostgreSQL obtains the system name of the user and verifies it against the database privileges. To enforce password authentication from local users, you must edit the pg_hba.conf file. Run the following commands within the psql shell to determine the location of this file.

If you are the `pulsys` user

```bash
sudo su - postgres
```
Otherwise if you are still logged in as the `postgres` user

```bash
psql
```
This will log you into the postgresql shell

```sql
SHOW hba_file;
```

Postgres shows you the location of the configuration file

```sql
hba_file
-------------------------------------
 /etc/postgresql/12/main/pg_hba.conf
(1 row)
```
Exit PostgreSQL with the `\q` meta-command, and return to the Linux shell.

As the `pulsys` user edit the pg_hba.conf file to relax authentication. Find the local line under “Unix domain socket connections only” and change the METHOD attribute from `peer` to `trust`. We will also want to change the `127.0.0.1/32` from `scram-sha-256` to `trust` 

Restart PostgreSQL to apply the new access rule.

```bash
sudo systemctl restart postgresql.service
```

As the `pulsys` user try to connect to your database now with 

```bash
psql -h 127.0.0.1 -U postgres
```

Postgresql is one of the rare open source project with fantastic documentation. [Read More here](https://www.postgresql.org/docs/14/tutorial.html) 

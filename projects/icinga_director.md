### Icinga Director installation

The project [documentation](https://icinga.com/docs/icinga-director/latest/doc/02-Installation/Ubuntu/) will be our guide

  1. Install the software with the following command:
     ```bash
     sudo apt -y install icinga-director
     ```

  2. Set up a database with the following steps:
     ```bash
      sudo su -l postgres
      createuser -P director # save password to lastpass
      createdb -E UTF8 --locale en_US.UTF-8 -T template0 -O director director
      psql director <<<'CREATE EXTENSION IF NOT EXISTS pgcrypto;'
      ```


# Don't Panic

## BrownField Migrating to the Private Network

  * ssh into the machine you want to migrate and get the network information with the following steps:
    ```bash
    ssh pulsys@machine-name-staging1.princeton.edu
    ip a
    ```

  * This information will be needed for removing firewall rules so save it

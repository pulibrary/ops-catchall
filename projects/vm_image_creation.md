### Create an Ubuntu Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on VMware

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)

#### Download qemu and the Ubuntu ISO File

  * Get and install QEMU and virt-manager
  ```bash
  sudo apt -y install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
  ```
  * Download the [Ubuntu LTS](https://ubuntu.com/download/server) iso file (Use Jammy (22.04))

#### Create a Linux Virtual Machine

  * Launch the virt-manager with `sudo virt-manager` on your system. Click on the **Create a New Virtual Machine** and Follow the Wizard.
    * The New Virtual Machine Wizard window will launch. Choose **Local install media (ISO image or CDROM)** option.
    * Select the Ubuntu ISO from the step above.
    * Configure memory and CPU settings.
      * 2 CPUs and 8196 MB
      * 30.0 GiB (makes our image free on AWS and Google Cloud)
    * Name will be 2024_jammy_{season}
    * Select Customize *Before Installation*
      * Remove Sound
      * Remove Tablet
      * Remove USB Redirector{1,2}
      * Change VirtIO Disk 1 to use *VirtIO Driver*
    * Select *Begin Installation*
  * Complete the Ubuntu installation with the steps below

#### Install Ubuntu on the Virtual Machine

The newly created Ubuntu virtual machine will appear in the virtual machine list on the left side. Select it and then click on the Play virtual machine option.

  * The Ubuntu GRUB menu will appear. Highlight the **Try or Install Ubuntu** option and press Enter. The Ubuntu installer will launch.
  * Click on the **Install Ubuntu** button. Keep the keyboard layout as default and select **Done**
  * Next, choose **Ubuntu Server** (the default). Proceed by clicking **Done**.
  * **IMPORTANT** (write down the resulting IP address) then select **Done**
  * Select the defaults (empty) for the proxy address then select **Done**
  * On the Guided storage configuration menu leave the defaults of **Use an entire disk** and **Setup this disk as an LVM group** (if this is for our VSphere VMs)
    * If this is for our cloud environment uncheck the **Set up this disk as an LVM group**
  * Whatever option you select above select done.
  * You will be warned of the potential destructive action that erases the disk, select **Continue**
  * Create the user profile on the next window.
    * Your Name: Pul Systems
    * Your servers name: lib-vm
    * Pick a username: pulsys
    * Choose a password: 1234temp (you will be changing this password later)
    * Confirm your password: same password as above
    * Select **Done**
  * Skip the Ubuntu Pro select and select **Continue**
  * Select **Install OpenSSH server** and select **Done**
  * Skip the **Featured Server Snaps** and select **Done**
  * When this is complete select **Reboot** (you will get errors on failure to unmount you CDROM which you can ignore by selecting the **ENTER** button)

#### Customize your Virtual Machine

When your machine reboots you can now create a bare minimal installation.

  * From your terminal log into your VM with `ssh pulsys@<the IP address you wrote down>`
    * if you forgot to record your IP address you can find it from the virt-manager application (it is a clunky UI).
    * you can also use `ip a` to find the results from the virb0 device 
  * Update your VM with `sudo apt -y update && sudo apt -y upgrade`
  * Install VMWare tools with `sudo apt -y install open-vm-tools`
  * Install fail2ban with `sudo apt -y install fail2ban`
    * Configure fail2ban for ssh with the following config at `/etc/fail2ban/jail.d/jail_ssh.local.conf`
    ```ini

    [sshd]
    filter = sshd
    action = iptables-multiport
    logpath = /var/log/auth.log
    maxretry = 5
    ```
  * Install vim and make it the default text editor with the following steps:
    ```bash
    sudo apt -y install vim
    sudo update-alternatives --config editor
    ```
    Select vim.basic in the resulting screen
  * Edit the sudoers file by running the following command: `sudo visudo`
    * this will launch the vi editor. Find the line that has `%sudo  ALL=(ALL) ALL` and modify it to look like this `%sudo ALL=(ALL) NOPASSWD:ALL` 
  * Restrict access to just the pulsys user with the following.
    * Open the `/etc/ssh/sshd_config` file and add the following line at the end of the file. `AllowUsers pulsys`
    * Add the [Ansible Tower Key](https://github.com/pulibrary/princeton_ansible/blob/main/keys/TowerKey.pub)
      Add the Operations Team keys to the VM in addition to the key above
      ```bash
      #
      ## Tower key goes on all machines
      # Key for our Tower instance
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP+lnexM16ZDOV113XlSHO2cgiGiO4bE3/IdFYkbv0RL pulsys@ansible-tower1.princeton.edu
      ## ops staff keys go on all machines
      # Keys for Operations Staff
      # Keys for acozine
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJfzzsSCWft0+Y5BBEgczdCQt9SIdU2Zft0aPiR3PRSR
      # Keys for beck-davis
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBItBQDPb3UWItqYUtURAlNjwtFyk5Hz7WYOLWGkfxUk
      # Keys for dphillips-39.keys
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIjsG/xtqi+W0qToHig+zuGKbPXQCj+ngXFj+SvzMAdD
      # Keys for gpmenos

      # Keys for jkazmier-PUL


      # Keys for kayiwa
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDtlzGWWoWGt/rxCpRRO9oV7a3NquyNWgUmlsg9wtCb854GFXysZUqCv6ykvWZUtzKyD/8zaSmcr6YRKcgtyUgsAMr3i5Vf1poeVFrLc9WkY7EXET3ADkCafbRzgITt1ISMhWLylZW7hmN+FRRZGs9csqwnbwoTEfk+/2VQJoITbBOAB+G+G+w4uLeKpQucZsDgwC0Crx103e/Ni5HiSHk4AGCs9KfhL69Bt5P41MGx4THpJzz7NMrassv5Ahp20EbkWv+AEAI7wEkH2tEH4WV1UFVLx+axDXUjfqtnkYnqb3PKXbA8cDTNAj8p7yV4gBSBdd6HqCmi9b4zfXrVRgEgffwFuYIsgSFu0c4G7V2kUzuedP7ZfukLWP9fYofRA8kpzRAHtHl6P08kA5pDhVy69Bf88PWWHm69SJbPKRC2InHYiWuGVrNtjfWsWHACNGlww1TrlpvHrGLXNTJoRNGMdUszwFOkRzNBU9lJZ+qToFxNHDhXJiASi8ZPl/7VUgvvnK/Wbp99iBeTbBCA2iBL/mQKXRLX0OFwua8xTbf6urzlRHE1Y6hiqdBdk/y9ppndAfkl5G6QmPxl79Ngg0amTZazHfAb9AAcnak6bsO1/Uyeki2ZbFjymFzbUpc6HcDy6qDHUrsYoqpxVhiUlahfdpqa8qw8kCeKeJxL8e5KEw==
      ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKRnDiuSOBsUFmv5II+K+DekV6NQGgv+DWL94IC8QY6LNa3xgJmKnnb2n0pwTXvJrIYx5DtHiCUaSKaGUYjgXww=
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCmASrWQMw4fHOwbjTLKXHakFC2fG3AKCfTbE8FVDuVRgUuxUTcs5ZiQGoEC+ffokrex6gf5u8kFc92leZHtGPXe765lGxNbDHvFFw+s2Eg6fBG85gQ0TWnWHSEvVhJGjbCMEk7BLRFrAx96u3dwAM5DCnz0hWx/O1jGkEIdGOCRI9DacnSytSR6Vsk0J/qIpT+IxdMAUTApapBWyr8x3gnUz8JeIZGa0HZB3UczSydNtCHCK2OZjh52sQpwrT6fiHDRlhQ1lcVcEwZN3J2ZYVOJ19/jdkp27uMds2NYwhrY7Ag0/i7wKEhO3wDQpomO1jmOoaEEbQ8HNnBp6eua3bc0hJSGmuwLPpeipAqTURRM9gBovgV1nSvY+hNXOKKRwcIPPuIQNCk3xn1Ry4BiE278LZoyliA1xqN/UslqoGLODhqwhO+F2Z91HGZZsgGH0Fip4TyVNRsOTNrehnGOAstOVA6bQkqX3dGw2drQAqx6YvaN0LOhwpKpboBpkHkOo9L4RN5SMckUycUjG6tnyIvanMNaigWKGuVPMjzo6ea0LSwVeuYJBEj/6LQnWUwRUxhpVbwxBahLlkzzpKwjzlhXxa3iCEONaK5G9Ay8PlTXK7VZQeHby1UwpOIr73H+FZNjxF3HIlPhDhdhfFUusB4DpOlmgiP11uJvoW3/Sq6Fw==
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDT/YSMGz9LutWtO1boqqYQnXLSeKIUzio9W2G2f09JG
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINx7YvulTKJDj8pwBcvol3bCpkm1hFSozGVD7MOk/qfN
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC8KUzLrdkC96xrzX0ZazKhj4mLe3XrLIXkzPSLUDK3fzYrqMQVRGkNRXZygYuMqhmVCHHeCQg28NjQew5h095QLC4Tso6YlVRq4p/v6zHXBze6pRxwSyK/mJLWBNdquS2XFMgq4vlvggd8w52dkWh8FUuZ96q6uRS3eHwA4tjyWMg+0s92Wml3d8p0S0teh8P+ytXIM1LLuyY3nVrmXeeqSxe9EAc+m4xdIMNkrPOIeyTl2zbSAk7bPLtWRSfKB7aqLRYmehTlfJ3WbxE/5D5GEcEMMdKtvfzh9gKKUHdzRXHs+AoLZlwXFgQadPCjTzECujMJA8q0lLVXIkbx63+nLxGGXdMxT/H6zfMCk0ayWr+UAEd6yGcSdaNWCbfljqDH7db+yj3MEBzWkH7O/2OLLQaKb0Ij9XSuDAYCLR18h48aar/Bwhwe1U1dK9R8ZvsknnF2rddz8SwblAWDddJk4pac39f00XeiJjRQuRGJtrlzAK9MEz4bVl2PfiZ+T6gJ5hEZ8qEY6KLLFcEiZE08a9ZOFI7mra1miB5K5sbnm940EnzQwG/aCqI7PBRoRNr0Wa2sBooQbKz0upohHtIDxnjb+4FhgdkyDGY0gF67lfv0zNyhUM+RfT11kX23JkDL3UB/PmcM93WLqdlK2R1/OLP7Hn5c41U/UHc71ZXRxQ==
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHgR+hOR3SRrOy9OjVW3Gi6U+tID2W6IJBDKWQOQPI2E
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMh9FC86vycV4LLlz6JugrqudLfkRi4j10Kl5kqJPY1G
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFdddOwvM3HsvW3sC0PPbP4Pq7q1AQ0anINFtgvcOsqI
      # Keys for VickieKarasic
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKDUPnOTVebrlXOOu7t1P2Wv+SB6rMC4jOEtqsaR8MVZ
      ```
#### Modify the network interface

The qemu image will need a different network driver when mounted on a ESXI host. Modify the netplan device with the following `sudo vim /etc/netplan/00-installer-config.yaml` and replace the `ensp1*` with `ens192` that the ESXi expects. Close and save the file

#### Strip out unique data

  * Cleanup current ssh-keys
    * Create a file names `regenerate_ssh_keys.sh`
    * Add the following to the file
    ```bash
    #!/usr/bin/bash

    # Script to regenerate SSH keys on first boot

    # Define paths to the ssh key files
    SSH_KEYS_PATH="/etc/ssh"

    echo "Regenerating SSH keys..."

    # Remove old SSH keys
    rm -f $SSH_KEYS_PATH/ssh_host_*

    # Regenerate new SSH keys
    dpkg-reconfigure openssh-server

    echo "SSH keys regenerated."

    # Disable this script after running once
    systemctl disable regenerate-ssh-keys.service

    ```
  * Make the script executable and move it to `/usr/local/sbin/`
    ```bash
    chmod a+x regenerate_ssh_keys.sh
    sudo mv regenerate_ssh_keys.sh /usr/local/sbin/regenerate_ssh_keys.sh
    ```
  * Create a systemd service file at `/etc/systemd/system/regenerate-ssh-keys.service` with the following:
    ```ini
    [Unit]
    Description=Regenerate SSH keys on first boot
    After=network.target

    [Service]
    Type=oneshot
    ExecStart=/usr/local/sbin/regenerate_ssh_keys.sh
    RemainAfterExit=yes

    [Install]
    WantedBy=multi-user.target

    ```
  * Enable the service to it runs at boot with:
    ```bash
    sudo systemctl enable regenerate-ssh-keys.service
    ```
  * Clean up apt with the following command `sudo apt clean`
  * Go to LastPass and find the password for the pulsys user and change it using the following as the `pulsys` user. 
    * passwd
    * You will be prompted for the 1234temp (current password)
    * Enter the password from lastpass or create a new one and save it on lastpass
  * shutdown your VM with `sudo shutdown -h now`

#### Export your VM

  * With your Virtual Machine powered select the **File Menu**
    * Select File -> View Manager (You should see your Ubuntu VM)
    * Select View Machine Details and perform any additional edits
  * You can now copy the image by running the following:
    ```bash
    sudo qemu-img convert -f qcow2 -O vmdk /var/lib/libvirt/images/2024_jammy_<season>.qcow2 /home/<username>/Desktop/2024_jammy_<season>.vmdk
    ```
  * When the export is complete upload your new image to the [Virtual Machine Image directory](https://drive.google.com/drive/u/0/folders/1Op-tNRvE_LMlJa6E-Ig4nNEKtKCcXsIF)
  * You can now follow the [VSphere Steps](vsphere_hypervisor.md) to import
      

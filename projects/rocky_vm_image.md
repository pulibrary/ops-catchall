### Create a Rocky Linux Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on QEMU

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)

#### Download qemu and the Ubuntu ISO File

  * Get and install QEMU and virt-manager
  ```bash
  sudo apt -y install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
  ```
  * Download the [Rocky Linux](https://rockylinux.org/download) iso file (Use Rocky Linux (9))

#### Create a Linux Virtual Machine

  * Launch the virt-manager with `sudo virt-manager` on your system. Click on the **Create a New Virtual Machine** and Follow the Wizard.
    * The New Virtual Machine Wizard window will launch. Choose **Local install media (ISO image or CDROM)** option.
    * Select the Ubuntu ISO from the step above.
    * Configure memory and CPU settings.
      * 2 CPUs and 8196 MB
      * 30.0 GiB (makes our image free on AWS and Google Cloud)
    * Name will be 2024_rocky_{season}
    * Select Customize *Before Installation*
      * Remove Sound
      * Remove Tablet
      * Remove USB Redirector{1,2}
      * Change VirtIO Disk 1 to use *VirtIO Driver*
    * Select *Begin Installation*
  * Complete the Rocky 9 installation with the steps below

#### Install Ubuntu on the Virtual Machine

The newly created Rocky virtual machine will appear in the virtual machine list on the left side. Select it and then click on the Play virtual machine option.

  * The Rocky GRUB menu will appear. Highlight the **Try or Install Rocky** option and press Enter. The Rocky installer will launch.
  * Click on the **Install Rocky Linux 9.4** button. Keep the keyboard layout as default and select **Continue**
  * Click on the **Installation Destination** button.
    * On the `Device Selection` menu select the 30GiB destination, 
    * Select the `Custom` radio button to customize your installer.
    * Create a `boot` mount point of 1GiB
    * Create a `swap` partition of 2GiB
    * Create `/` mount point of 29GiB then click **Done**
      * Accept the changes you made
  * Click on the **Software Selection** button.
    * It defaults to *Server with GUI* change that to **Server**, then click **Done**
  * Click on the **User Creation** button.
    * Full name: Pul Systems
    * User name: pulsys
      * check: **Make this user administrator**
    * Password: 1234Temp (You will be changing this later)
      * Proceed by clicking **Done** two times because of the weak password
  * Click on the **Network & Host Name** button.
    * change `Host Name` from localhost to `lib-vm` and click on **Apply**, then click **Done**
  * When this is complete select **Begin Installation**
  * When this is done, click on the **Reboot** button

#### Customize your Virtual Machine

When your machine reboots you can now create a bare minimal installation.

  * From virt-manager window login with the pulsys user
    * run `ip a` and get the ip address of your vm
  * From your terminal log into your VM with `ssh pulsys@<the IP address you wrote down>`
  * Update your VM with `sudo dnf -y update`
  * Install VMWare tools with `sudo dnf -y install open-vm-tools`
  * Install fail2ban with `sudo dnf install epel-release -y && sudo dnf -y install fail2ban`
    * Configure fail2ban for ssh with the following config at `/etc/fail2ban/jail.d/jail_ssh.local.conf`
    ```ini
    [sshd]

    enabled = true
    filter = sshd
    action = iptables-multiport
    logpath = /var/log/secure
    maxretry = 5

    ```
  * Edit the sudoers file by running the following command: `sudo visudo`
    * this will launch the vi editor. Find the line that has `#%wheel  ALL=(ALL) NOPASSWD:ALL` and uncomment it to look like this `%wheel ALL=(ALL) NOPASSWD:ALL` 
    * comment out the line with `%wheel ALL=(ALL) ALL`
  * Restrict access to just the pulsys user with the following.
    * Open the `/etc/ssh/sshd_config` file and add the following line at the end of the file. `AllowUsers pulsys`
    * Add the [Ansible Tower Key](https://github.com/pulibrary/princeton_ansible/blob/main/keys/TowerKey.pub)
    * create the `.ssh/authorized_keys`
    ```bash
    mkdir ~/.ssh
    chmod 700 ~/.ssh
    touch ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
    ```
  * Add the Operations Team keys to the VM in addition to the key above

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

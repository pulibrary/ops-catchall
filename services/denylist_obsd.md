### Create a denylist OpenBSD Virtual Machine

**Don't Panic!**

These steps will setup a [denylist](https://denylist.pulcloud.io) VM which will be configured and managed by [https://github.com/pulibrary/princeton_ansible/blob/main/playbooks/denyhost.yml](https://github.com/pulibrary/princeton_ansible/blob/main/playbooks/denyhost.yml)

#### Prerequisites to Install OpenBSD on (Google Cloud)

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)

#### Download VMware and the OpenBSD ISO File

  * Get and install [VMWare Workstation](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html) (As of this Spring 2024 writing we still need amd64)
  * Download the [OpenBSD Release](https://cdn.openbsd.org/pub/OpenBSD/7.5/amd64/install75.iso) iso file (it is 7.5 in 2024 Spring)

#### Create an OpenBSD Virtual Machine

* Launch VMware Workstation Player on your system. Click on the **Create a New Virtual Machine** option present on the home page.
    * The New Virtual Machine Wizard window will launch. Click on **Custom (advanced)** option and then click **Next.**
    * The Virtual Hardware Compatibility window will show. Select (as of Spring 2024) **ESXi 7.0** then click **Next**
    * The Install operating system from window will launch. Select **Use ISO image** and browse to the location you downloaded the OpenBSD Release iso file above then click **Next**
      * From the Guest Operating System window select Other **4** the defaults of **Linux** and **Other 64-bit** then click **Next**
    * The Virtual Machine Name window will launch. Give the Virtual Machine a name (e.g pul_obsd_image) and select the resulting **Location** it creates and click **Next**
      * From the Processors window select the defaults of **2 processors** and **1 core** per processor, then click **Next**
    * From the Memory window slide the scale to **4 GB** of memory or enter **4096** in the box then click **Next**
    * From the Network Connection window select the defaults of **Use network address translation (NAT) option, then click **Next**
    * From the I/O Controller Types select the defaults of **LSI Logic (Recommended)** option, then click **Next**
    * From the Virtual Disk Type window select the defaults of **SCSI (Recommended)** option, then click **Next**
    * From the Disk window select the defaults of **Create a new virtual disk** option, then click **Next**
    * From the Disk Size window increase the disk size to *30 GB** then click **Next**
    * From the Disk File window you should have the name you created when you gave the virtual machine a name (e.g. pul_obsd_image.vmdk) click **Next**
  * From the Ready to Create a Virtual Machine window, select the **Customize Hardware** option.
    * Remove the **Sound card** and **USB Controller** 
    * Click the **Finish** button

#### Install OpenBSD on the Virtual Machine

The newly created OpenBSD virtual machine will appear in the virtual machine list on the left side. Select it and then click on the Play virtual machine option.

  * The OpenBSD install menu will appear. Select **I** **(I)nstall** and press Enter. The OpenBSD install start.
  * Next, Choose **your keyboard layout** (the default). Proceed by clicking **Enter**.
  * Next, Enter your **System hostname**. Proceed by typing `lib-vm` and pressing **Enter**.
  * Next, Enter your **Network interface to configure?** (the default is em0). Proceed by clicking **Enter**.
  * Next, Enter your **IPv4 to configure** (the default). Proceed by clicking **Enter**.
  * Next, Enter your **IPv6 to configure** (the default). Proceed by clicking **Enter**.
  * Next, Enter your **Network interface to configure** (the default is done). Proceed by clicking **Enter**.
  * Next, Enter your **Password for root account** which (will not echo) Choose a password: 1234temp (you will be changing this password later)
  * Next, Accept **Start sshd(8) by default** (the default is yes) Proceed by clicking **Enter**.
  * Next, decline **Do you expect to run the X Window System** (the default is yes) Proceed by clicking **Enter**.
  * Next, create the pulsys user **Setup a user? (enter a lower-case loginname)** (the default is no) Enter the username `pulsys` and proceed by clicking **Enter**.
  * Next, Enter the Full pulsys user name on **Full name for user pulsys?** (the default is no) Enter the names `Pul Systems` and proceed by clicking **Enter**.
  * Next, Enter your **Password for user pulsys** which (will not echo) Choose a password: 1234temp (you will be changing this password later)
  * Next, no password for root at **Allow root ssh login** (default is no) select *prohibit-password* and click **Enter**
  * Next, Set our Time Zone at **What timezone are you in?** (the default is US/Eastern) Proceed by clicking **Enter**.
  * Next, Partition our Disk **Which disk is the root disk?** (the default is wd0) Proceed by clicking **Enter**.
  * Next, Encrypt our Disk **Encrypt the rook disk with a passphrase** (the default is no) Proceed by clicking **Enter**.
  * Next, Use our whole disk **Use (W)hole disk MBR** (the default is whole) Proceed by clicking **Enter**.
  * Next, Use Auto layout on the disk **Use (A)uto layout** (the default is a) Proceed by clicking **Enter**.
  * Next, Install the sets the disk **Location of sets?** (the default is cd0) Proceed by clicking **Enter**.
  * Next, set the path name of the install sets the disk **Pathname to the sets?** (the default is 7.5/amd64 in Spring 2024) Proceed by clicking **Enter**.
  * Next, set the names **Set name(s)?** (the default is done) Proceed by clicking **Enter**.
  * Next, Ignore the lack of a signed file **Directory does not contain SHA256.sig** (the default is no) Enter `yes` then proceed by clicking **Enter**.
  * Next, set the names **Set name(s)?** (the default is done) Proceed by clicking **Enter**.
  * Next, set the Time **Time appears wrong** (the default is the correct time) Proceed by clicking **Enter**.
  * Next, Reboot **Exit to (S)hell** (the default is reboot) Proceed by clicking **Enter**.

#### Customize your Virtual Machine

When your machine reboots you can now create a bare minimal installation.

  * Login as root from the VMWare Workstation application (it is a clunky UI). Once you are logged in `ifconfig` and go back to using the terminal app.
    * use the results of the `em0` to login from your Konsole terminal with the following. `ssh pulsys@<ip address>`
    * change the root password and save it to lastpass. The first step below will need the previous simple password of `1234temp` and the second step changes the root password
    ```bash
      su 
      passwd
    ```
      the password does not echo in the steps above. 
    * enable pulsys user to use the `doas` command. (OpenBSD does not use sudo)
      ```bash
      cp /etc/examples/doas.conf /etc/doas.conf
      ```
      add the following line to the end of the new `/etc/doas.conf` `permit nopass :wheel`
  * exit the root account with an `exit`
    * install vim with the following command. `doas pkg_add -vi neovim`
    * install python (we need it for ansible) with the following command. `doas pkg_add -vi python`
      * select the newest version of python at the prompt
  * Go to LastPass and find the password for the pulsys user and change it using the following as the `pulsys` user. 
    * `passwd`
    * You will be prompted for the 1234temp (current password)
    * Enter the password from lastpass or create a new one and save it on lastpass
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

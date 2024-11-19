### Create a Rocky Linux Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on VMWare Workstation

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)
    * Download [VMware's Ovftool](https://github.com/rgl/ovftool-binaries) and add it to your path. (e.g ~/.local/)
    ```bash
    cd ~/Downloads
    unzip VMware-ovftool-4.4.3-18663434-lin.x86_64.zip
    mv ~/Downloads/ovftool ~/.local
    ```
  * add to your `~/.zshrc`
    ```bash
    # ovftool
    export PATH=$HOME/.local/ovftool:$PATH
    ```

#### Download VMWare Workstation and the Ubuntu ISO File

  * You can download (registration with Broadcom required) and Install [VMWare Workstation Pro](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
  * Download the [Rocky Linux](https://rockylinux.org/download) iso file (Use Rocky Linux (9))

#### Create a Rocky Linux Virtual Machine

  * Launch the VMWare Workstation on your system. **Select File > New** and Follow the Wizard.
    * In the **New Virtual Machine Wizard** window, Select **Custom (advanced)**
    * In the **Choose the Virtual Machine Hardware Compatibility** window, select **ESXi 7.0**
    * In the **Guest Operating System Installation** window. Choose ** Use (ISO image or CDROM)** option. Select the Rocky ISO from the step above. (You will get ESXi does not support Rocky Linux warning)
      * Select the Linux —> CentOS 8 64-bit
    * In the **Name the Virtual Machine** window, Name: `(year)-rocky-(season)-template` and take note of the Location.
    * In the **Processor Configuration** window.
      * 2 Processors
      * Number of cores per processor 1
    * In the **Memory for Virtual Machine** window.
      * Toggle the memory to 8GB (precise value is 8192)
    * In the **Network Type** window.
      * Select the default, (Use Network address translation NAT)
    * In the **Select I/O Controller Types** window.
      * Select the default, (LSI Logic (Recommended))
    * In the **Select a Disk Types** window.
      * Select the default, (NVMe Recommended)
      * Select the default, Create a new virtual disk
    * In the **Specify Disk Capacity** window.
      * Disk Size, (30.00)
      * Split Virtual Disk into multiple files
    * In the **Specify Disk File** window.
      * Select the default
     * In the **Ready to Create Virtual Machine** window.
      * Click on Customize Hardware
        * Remove Sound Card, USB Controller, 
  * Complete the Rocky 9 installation with the steps below

#### Install Rocky on the Virtual Machine

  * The Rocky GRUB menu will appear. Highlight the **Try or Install Rocky** option and press Enter. The Rocky installer will launch.
  * Click on the **Install Rocky Linux 9.4** button. Keep the keyboard layout as default and select **Continue**
  * Click on the **Installation Destination** button.
    * Select the default selected 30GiB destination
    * Select the Automatic Storage Configuration, then click **Done** 
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
  * Install perl and dhclient  with `sudo dnf -y install dhclient perl NetworkManager`
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
  * Update Core ReadyBuilder with `sudo /usr/bin/crb enable`
  * Edit the sudoers file by running the following command: `sudo visudo`
    * this will launch the vi editor. Find the line that has `#%wheel  ALL=(ALL) NOPASSWD:ALL` and uncomment it to look like this `%wheel ALL=(ALL) NOPASSWD:ALL` 
    * comment out the line with `%wheel ALL=(ALL) ALL`
  * Restrict access to just the pulsys user with the following.
    * Open the `/etc/ssh/sshd_config` file and add the following line at the end of the file. `AllowUsers pulsys`
    * create the `.ssh/authorized_keys`
    ```bash
    mkdir ~/.ssh
    chmod 700 ~/.ssh
    touch ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
    ```
  * Restrict access to just the pulsys user with the following.
    * Open the `/etc/ssh/sshd_config` file and add the following line at the end of the file. `AllowUsers pulsys`
    * Add the [Ansible Tower Key](https://github.com/pulibrary/princeton_ansible/blob/main/keys/TowerKey.pub) to by editing the `/home/pulsys/.ssh/authorized_keys` file.
      # Key for our Tower instance
      Grab the Ops Team Keys from Github and append those to `/home/pulsys/.ssh/authorized_keys`:
        - [acozine](https://github.com/acozine.keys)
        - [beck-davis](https://github.com/beck-davis.keys)
        - [dphillips-39](https://github.com/dphillips-39.keys)
        - [gpmenos](https://github.com/gpmenos.keys)
        - [jkazmier-PUL](https://github.com/jkazmier-PUL.keys)
        - [kayiwa](https://github.com/kayiwa.keys)
        - [VickieKarasic](https://github.com/vickieKarasic.keys)

#### Strip out unique data

  * Cleanup current ssh-keys
    * Create a file named `regenerate_ssh_keys.sh`
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
    ssh-keygen -A

    echo "SSH keys regenerated."

    systemctl restart sshd

    ```
  * Make the script executable and move it to `/etc/rc.d/`
    ```bash
    chmod a+x regenerate_ssh_keys.sh
    sudo mv regenerate_ssh_keys.sh /etc/rc.d/rc.local
    ```
  * Go to LastPass and find the password for the pulsys user and change it using the following commands as the `pulsys` user. 
    * `passwd`
    * You will be prompted for the 1234temp (current password)
    * Enter the password from lastpass or create a new one and save it on lastpass
  * shutdown your VM with `sudo shutdown -h now`

#### Export to OVA for ESXi
- Power off the VM.
- In VMware Workstation
  - Select `(year)-jammy-(season)-template` -> Settings from the step earlier
    - Change the (CD/DVD) to **Use a physical drive** Device Auto detect and save
  - Go to File --> Export to OVF.
- Save the OVA template file.
- When the export is complete upload your new image to the [Virtual Machine Image directory](https://drive.google.com/drive/u/0/folders/1Op-tNRvE_LMlJa6E-Ig4nNEKtKCcXsIF)
- You can now follow the [VSphere Steps](vsphere_hypervisor.md) to import


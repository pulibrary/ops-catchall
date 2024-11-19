### Create an Ubuntu Virtual Machine

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
  * Download the [Ubuntu LTS](https://ubuntu.com/download/server) iso file (Use Jammy (22.04))

#### Create an Ubuntu Jammy Virtual Machine

  * Launch the VMWare Workstation on your system. **Select File > New** and Follow the Wizard.
    * In the **New Virtual Machine Wizard** window, Select **Custom (advanced)**
    * In the **Choose the Virtual Machine Hardware Compatibility** window, select **ESXi 7.0**
    * In the **Guest Operating System Installation** window. Choose ** Use (ISO image or CDROM)** option. Select the Ubuntu ISO from the step above.
    * In the **Name the Virtual Machine** window, Name: `(year)-jammy-(season)-template` and take note of the Location.
    * In the **Processor Configuration** window.
      * Select the default, 2 Processors
      * Select the default, Number of cores per processor 1
    * In the **Memory for Virtual Machine** window.
      * Toggle the memory to 8GB (precise value is 8192)
    * In the **Network Type** window.
      * Select the default, (Use Network address translation NAT)
    * In the **Select I/O Controller Types** window.
      * Select the default, (LSI Logic (Recommended))
    * In the **Select a Disk Types** window.
      * Select the default, (SCSI Recommended)
      * Select the default, Create a new virtual disk
    * In the **Specify Disk Capacity** window.
      * Disk Size, (30.00)
      * Split Virtual Disk into multiple files
    * In the **Specify Disk File** window.
      * Select the default
     * In the **Ready to Create Virtual Machine** window.
      * Click on Customize Hardware
        * Remove Sound Card, USB Controller
        * In the Display Uncheck (Accelerate 3d graphics) 
  * Complete the Jammy installation with the steps below


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
    * Open your editor to create a file named `regenerate_ssh_keys.sh`
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
  * Clean up apt and cloud init with the following commands:
    ```bash
    sudo apt clean
    ```
  * Go to LastPass and find the password for the pulsys user and change it using the following as the `pulsys` user. 
    * passwd
    * You will be prompted for the 1234temp (current password)
    * Enter the password from lastpass or create a new one and save it on lastpass
  * shutdown your VM with `sudo shutdown -h now`

#### Export to OVA for ESXi
- Power off the VM.
- In VMware Workstation
  - Select `(year)-jammy-(season)-template` -> Settings
    - Change the (CD/DVD) to Auto detect and save
  - Go to File --> Export to OVF.
- Save the OVA template file.
- When the export is complete upload your new image to the [Virtual Machine Image directory](https://drive.google.com/drive/u/0/folders/1Op-tNRvE_LMlJa6E-Ig4nNEKtKCcXsIF)
- You can now follow the [VSphere Steps](vsphere_hypervisor.md) to import

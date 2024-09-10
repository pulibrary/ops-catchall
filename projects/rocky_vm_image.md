### Create a Rocky Linux Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on QEMU

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)
  * Download [VMware's Ovftool](https://github.com/rgl/ovftool-binaries) and add it to your path. (e.g ~/.local/)

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
    * Create a `swap` partition of 4GiB
    * Create `/` mount point of 25GiB then click **Done**
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
  * Add the [Ansible Tower Key](https://github.com/pulibrary/princeton_ansible/blob/main/keys/TowerKey.pub) to the `~/.ssh/authorized_keys` file
  * Add the Operations Team keys to the VM in addition to the key above

 ##### Key for our Tower instance
 
  * Grab the Ops Team Keys from Github and append those to `/home/pulsys/.ssh/authorized_keys`:
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
  * Configure the network interface. You will need add the following configuration to the resulting Virtual Machine so it will boot on an ESXi host. Create a file here with `sudo vim /etc/NetworkManager/system-connections/ens192.nmconnection`
    ```file
    [connection]
    id=ens192
    type=ethernet
    interface-name=ens192
    [match]
    name=ens192
    [ethernet]
    [ipv4]
    method=auto
    [ipv6]
    method=ignore
    ```
  * Remove the network connection for the virtual machine with `sudo rm /etc/NetworkManager/system-connections/enp1s0.nmconnection`
  * Go to LastPass and find the password for the pulsys user and change it using the following as the `pulsys` user. 
    * passwd
    * You will be prompted for the 1234temp (current password)
    * Enter the password from lastpass or create a new one and save it on lastpass
  * shutdown your VM with `sudo shutdown -h now`

#### Export your VM

  * With your Virtual Machine powered select the **File Menu**
    * Select File -> View Manager (You should see your Rocky VM)
    * Select View Machine Details and perform any additional edits
  * You can now copy the image by running the following:
    ```bash
    sudo qemu-img convert -f qcow2 -O vmdk /var/lib/libvirt/images/2024_rocky_<season>.qcow2 /home/<username>/Desktop/2024_rocky_<season>.vmdk
    ```
  * convert the qcow2 file to a vsphere compatible vmdk by creating the `2024_rocky_<season>.vmx` file

    ```file
    .encoding = "UTF-8"
    config.version = "11"
    virtualHW.version = "17"
    memsize = "8196"
    numvcpus = "2"
    scsi0.present = "TRUE"
    scsi0:0.present = "TRUE"
    scsi0:0.fileName = "2024_rocky_<season>.vmdk"
    ethernet0.present = "TRUE"
    ethernet0.connectionType = "nat"
    guestOS = "rhel9_64Guest"
    ```
  * make sure the file is saved to your Desktop directory then run `ovftool ~/Desktop/2024_rocky_<season>.vmx ~/2024_rocky_<season>.ovf`
  * When the export is complete upload your three new image files to the [Virtual Machine Image directory](https://drive.google.com/drive/u/0/folders/1Op-tNRvE_LMlJa6E-Ig4nNEKtKCcXsIF)
  * You can now follow the [VSphere Steps](vsphere_hypervisor.md) to import
      


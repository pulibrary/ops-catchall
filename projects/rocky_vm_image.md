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





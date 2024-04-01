### Create an Ubuntu Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on VMware

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)

#### Download VMware and the Ubuntu ISO File

  * Get and install [VMWare Workstation](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html) (As of this Spring 2024 writing we still need amd64)
  * Download the [Ubuntu LTS](https://ubuntu.com/download/server) iso file

#### Create a Linux Virtual Machine

  * Launch VMware Workstation Player on your system. Click on the **Create a New Virtual Machine** option present on the home page.
    * The New Virtual Machine Wizard window will launch. Click on **Custom (advanced)** option and then click **Next.**
    * The Virtual Hardware Compatibility window will show. Select (as of Spring 2024) **ESXi 7.0** then click **Next**
    * The Install operating system from window will launch. Select **Use ISO image** and browse to the location you downloaded the Ubuntu LTS iso file above then click **Next**
      * From the Guest Operating System window select the defaults of **Linux** and **Ubuntu 64-bit** then click **Next**
    * The Virtual Machine Name window will launch. Give the Virtual Machine a name (e.g pul_image) and select the resulting **Location** it creates and click **Next**
      * From the Processors window select the defaults of **2 processors** and **1 core** per processor, then click **Next**
    * From the Memory window slide the scale to **8 GB** of memory or enter **8192** in the box then click **Next**
    * From the Network Connection window select the defaults of **Use network address translation (NAT) option, then click **Next**
    * From the I/O Controller Types select the defaults of **LSI Logic (Recommended)** option, then click **Next**
    * From the Virtual Disk Type window select the defaults of **SCSI (Recommended)** option, then click **Next**
    * From the Disk window select the defaults of **Create a new virtual disk** option, then clieck **Next**
    * From the Disk Size window increase the disk size to *30 GB** then click **Next**
    * From the Disk File window you should have the name you created when you gave the virtual machine a name (e.g. pul_image.vmdk) click **Next**
  * From the Ready to Create a Virtual Machine window, select the **Customize Hardware** option.
    * Remove the **Sound card** and **USB Controller** 
    * Select the **Display** option on the left and uncheck the **Accelerate 3D graphics** button, then click **Close**
    * Click the **Finish** button

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

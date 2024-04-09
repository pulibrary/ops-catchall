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

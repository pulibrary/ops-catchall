### Deploy an Ubuntu Hypervisor

#### Connect to Vsphere

You will need to make sure you are on the princeton network and connected using remote desktop to our vsphere infrastructure with the following steps.

* Using your remote desktop connection application connect to `libstaff-NNN` using your PUL Credentials
* Using the browser on `libstaff-NNN` connect to either [https://lib-vmctr-dev.princeton.edu/](https://lib-vmctr-dev.princeton.edu/) or [https://lib-vmctr.princeton.edu/](https://lib-vmctr.princeton.edu/) for staging or production respectively
* You will be using your VI-Service account to make the connection above
* Accept the self-signed certificate

#### Deploy OVF Template

* From a separate tab or browser Download and save the OVF template files from [Google Drive](https://drive.google.com/drive/folders/0B0s-Lc6vvmqqTF9yaklHWW1RdmM?resourcekey=0-tdIXAZtmu1klMhHUMSE00g)
* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* On Staging Select the the *VMCluster* **Actions** and on Prod Select either *Library* or *Diglib* **Actions** with three ellipses at the top right hand panel
* You will select the `Deploy OVF Template...` which will launch a Wizard.
* Select the `Local file` radio button
* Select `Upload Files` option and navigate to where you have the 3 Files you saved above and press `NEXT`
* Give the `Virtual machine name:` a name (ideal that matches DNS)
* From `Select a location for the virtual machine` place the virtual machine in `Discovered virtual machine` for staging or `drdsetvms` on Production and press `NEXT`
* From `Select the destination compute resource for this operation` expand the `VMCluster` and select one of the hosts and press `NEXT`
* Select one of the **none host** storage locations (they will usually have SAN on them and not a host name). The preference is to always `thin provision` the `Select virtual disk format` from the drop down and press `NEXT`
* When Selecting the Network select `Virtual Machine Network` except in cases where a new VM will be in the private network and press `NEXT`
* Press `Finish`

#### Initial VM Configuration

The following steps are needed to complete your Virtual Machine setup.

 * Get the MAC address of the VM by right click (with the virtual machine highlighted) to edit settings. (You may need to ensure `CD/DVD drive 1` is set to *Client Device*) select `Network adapter 1` and expand to get the MAC address
 * Register the [MAC address](http://networkregistration.princeton.edu)
 * If this is a new machine make a PR on the [infrastructure repo](https://github.com/PrincetonUniversityLibrary/infra) to register the new Virtual Machine
 * Power On the Virtual Machine with a right click and select `Power On`
 * If your new Virtual Machine name will be `yourdns1.princeton.edu` You can now connect to the Virtual Machine with `ssh pulsys@yourdns1.princeton.edu`
 * You will `possibly` get a **MAN-IN-THE-MIDDLE** attack warning
 * Edit the host name at the following locations
   * substitute the name `lib-vm` on `/etc/hosts` and `/etc/hostname` with `yourdns1`
   * the following perl command will do that:

    ```zsh
     sudo perl -pi -e 's/lib-vm/yourdns1/g' /etc/hostname
     ```
     Then run the following:

     ```zsh
     sudo perl -pi -e 's/lib-vm/yourdns1/g' /etc/hosts
     ```

 * Update the VM by running `apt` command below:

   ```zsh
   sudo apt -y update
   sudo apt -y upgrade
   ```
 * When this is completed. Restart your Virtual Machine with `sudo /sbin/reboot`

#### Delete a Virtual Machine

Follow the steps above to Connect to VSphere

* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* Using the magnifying glass search next to the label vSphere Client for the Virtual Machine you wish to delete.
* The virtual machine will be selected on your results; right click and select `Power Off` and wait for the machine to power off.
* **This step could lead to disaster so be certain you want to do this**. Right click on the selected server again and select `Delete from Disk`

#### Kick (Reboot) a Virtual Machine

Follow the steps above to Connect to VSphere

* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* Using the magnifying glass search next to the label vSphere Client for the Virtual Machine you wish to delete.
* The virtual machine will be selected on your results; right click and select `Power Off` and wait for the machine to power off.
* Right click on the Virtual Machine again and Select `Power On` and wait for the machine to come back on. You may want to `Launch the Web Console` to see what is actually happening on the virtual machine.

#### Migrating Virtual Machines to Production or Staging

 * Find and power down the virtual machine you plan to migrate
 * Unregister the virtual machine from the inventory by right clicking on it to unregister
 * The `lib-vm-diglib4` connects to *VMSANVOLDEV00{1/2}* and can be used to migrate between the production and staging environments
 * Right click and migrate the virtual machine to either of the storage above
 * Locate your VM files be browsing the storage above (we will be searching for yourvm.vmx file)
 * Register the new VM in the environment you want

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
* On Staging Select the the *VMCluster* **Actions** and on Prod Select either *Library* or *Diglib* **Actions**
* You will select the `Deploy OVF Template...` which will launch a Wizard.
* Select the `Local file` radio button
* Select `Upload Files` option and navigate to where you have the 3 Files you saved above and press `NEXT`
* Give the `Virtual machine name:` a name (ideal that matches DNS)
* From `Select a location for the virtual machine` place the virtual machine in `Discovered virtual machine` for staging or `drdsetvms` on Production and press `NEXT`
* From `Select the distination compute resource for this operation` expand the `VMCluster` and select one of the hosts and press `NEXT`
* Select on of the **none host** storage locations. The preference is to always `thin provision` the `Select virtual disk format` from the drop down and press `NEXT`
* When Selecting the Network select `Virtual Machine Network` except in cases where a new VM will be in the private network and press `NEXT` 
* Press `Finish`

#### Delete a Virtual Machine

Follow the steps above to Connect to VSphere

* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* Using the magnifying glass search for the Virtual Machine you wish to delete.
* The virtual machine will be selected on your results; right click and select `Power Off` and wait for the machine to power off.
* This step could lead to disaster so be certain you want to do this. Right click on the selected server again and select `Delete from Disk` 

#### Kick (Reboot) a Virtual Machine

Follow the steps above to Connect to VSphere

* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* Using the magnifying glass search for the Virtual Machine you wish to delete.
* The virtual machine will be selected on your results; right click and select `Power Off` and wait for the machine to power off.
* Right click on the Virtual Machine again and Select `Power On` and wait for the machine to come back on. You may want to `Launch the Web Console` to see what is actually happening on the virtual machine.



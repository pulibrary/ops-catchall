### Deploy an Ubuntu Hypervisor

#### Connect to Vsphere

You will need to make sure you are on the princeton network and connected using remote desktop to our vsphere infrastructure with the following steps.

* Using your remote desktop connection application connect to `libstaff-NNN` using your PUL Credentials
* Using the browser on `libstaff-NNN` connect to either [https://lib-vmctr-dev.princeton.edu/](https://lib-vmctr-dev.princeton.edu/) or [https://lib-vmctr.princeton.edu/](https://lib-vmctr.princeton.edu/) for staging or production respectively
* You will be using your VI-Service account to make the connection above
* Accept the self-signed certificate

#### Deploy OVF Template

* From a separate tab or browser Download and save the vmdk file from [Google Drive](https://drive.google.com/drive/folders/0B0s-Lc6vvmqqTF9yaklHWW1RdmM?resourcekey=0-tdIXAZtmu1klMhHUMSE00g)
* On Staging Select *Library-Dev* and on Prod Select either *Library* or *Diglib*
* On Staging Select the the *VMCluster* **Actions** and on Prod Select either *Library* or *Diglib* **Actions** with three ellipses at the top right hand panel
* You will select the `Datastores` Tab.
  * Make a note of which of the datastore you pick. You will need this later
  * Upload the downloaded vmdk file from the step earlier. (You may get a prompt about invalid certificates. Just click on this)
* From the Actions menu select the `New Virtual Machine` option which will launch a Wizard.
* Give the `Virtual machine name:` a name (the format of YYYY-ubuntu-nickname-season-template) in the `Select a name and folder` and place the virtual machine in `Discovered virtual machine` and press `NEXT`
* From `Select a compute resource` place the virtual machine in one of the hosts `VMCluster` and press `NEXT`
* From `Select storage` pick the storage from the step earlier and press `NEXT`
* From `Select compatibility` choose `ESXi 7.0 U2 and later` and press `NEXT`
* From `Select a guest OS` choose `Linux` and `Ubuntu Linux (64-bit)` and press `NEXT`
* From `Customize Hardware` Delete `New Hard Disk` and select `ADD NEW DEVICE` and pick add `Add Existing Drive`. Locate where you uploaded the 2024-ubuntu vmdk file.
  * Expand the new drive you just add and from `Virtual Device Node` select `IDE 0`
* Change the memory to 8GB and press `NEXT`
* Press `Finish`
* Find your New `2024-ubuntu-<season>-template` and Select the `Actions` menu. Find the Template Submenu and Convert to Template

#### Initial VM Configuration

The following steps are needed to complete your Virtual Machine setup.

 * Get the MAC address of the VM by right click (with the virtual machine highlighted) to edit settings. (You may need to ensure `CD/DVD drive 1` is set to *Client Device*) select `Network adapter 1` and expand to get the MAC address
 * Register the [MAC address](http://networkregistration.princeton.edu)
 * If this is a new machine make a PR on the [infrastructure repo](https://github.com/PrincetonUniversityLibrary/infra) to register the new Virtual Machine
 * Power On the Virtual Machine with a right click and select `Power On`
 * If your new Virtual Machine name will be `yourdns1.princeton.edu` You can now connect to the Virtual Machine with `ssh pulsys@yourdns1.princeton.edu`

<details>
<summary>* You will `possibly` get a **MAN-IN-THE-MIDDLE** attack warning

</summary>

You may encounter this message in your shell when attempting to connect: 

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:CfV92vg1Slp+DvYHGeMItTV/N7dGsGBXc0h8A63RJ9s.
Please contact your system administrator.
Add correct host key in /Users/user_name/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /Users/user_name/.ssh/known_hosts:24
Host key for pul-sandbox.lib.princeton.edu has changed and you have requested strict checking.
Host key verification failed.
```
## Use this command to edit your known_hosts file

`vim ~/.ssh/known_hosts +"24d|x"` 

Notice that the line number corresponds to the text:
```
"Offending ED25519 key in /Users/user_name/.ssh/known_hosts:24"
```

You should now be able to ssh into your machine.
</details>
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

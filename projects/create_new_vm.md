
### Create a new Jammy VM 

- Search for "template" and open template_jammy_2023_11
    - Click on Actions and then New VM from This Template
        - Name is same as original VM
        - On dev, choose Library-Dev -> Discovered virtual machine
        - On prod, choose Library -> dev-rebootable-vms
        - Put on same compute resource as original VM
        - Put on storage that has the most space free
        - From "Select virtual disk format" drop-down, choose Thin Provision
        - Check boxes: "Customize this virtual machine's hardware" and "Power on virtual machine after creation"
        - Choose client device from CD/DVD drive 1 drop-down
        - Expand Network adapter 1, choose Manual MAC address and paste the 6 characters copied on the VM in step 1
        - Check that memory and CPU are the same here as on the original. If need to make a change, power off the VM, add memory or CPU, and then power back on.
    - Log in to new VM
        - Because this is the first time logging into the VM, you may get a warning about a man-in-the-middle attack, and you will need to edit the .ssh/known_hosts file to add your ssh keys at the line specified in the output you receive. Shortcut command to do this: ```vim ~/.ssh/known_hosts +"[line#]d|x"```
        - Change the name of the VM:
            ```sudo perl -pi -e s/lib-vm/[VM name]/g /etc/hosts```
            ```sudo perl -pi -e s/lib-vm/[VM name]/g /etc/hostname```
        - ```sudo apt -y update```
        - ```sudo apt -y upgrade```
        - ```sudo /sbin/reboot```

## Expand the disk size 

Our VMS will be using 14GB or the allocated thin provisioned 28GB. The following steps will expand it to utilize all of the space. The following steps will expand your initial disk size

- ```sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv```
- ```sudo resize2fs /dev/ubuntu-vg/ubuntu-lv```
- ```sudo /sbin/reboot```
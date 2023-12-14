### Get the mac address of the VM you are about to replace

- Log into VM from terminal
- ```ip a``` (copy last 6 digits [e.g. 1b.25.cc] of mac address)
- ```sudo /sbin/halt -p``` (to shut down)

### Create a Jammy VM on the same host you are replacing

- Rename the current VM with today's date (e.g. lib-vm-YYYY-MM-DD)
        - Check which compute resource it's on and note this
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

## Expand the disk size following the steps in https://github.com/pulibrary/pul-it-handbook/blob/main/services/disk_lvm_expansion.md

- Note: should only need to run first two commands in instructions above to expand initial disk size. If a new disk is needed, follow rest of steps
```sudo /sbin/reboot```

## Edit the group_vars .yml file 

- Check out a new branch ("i####_vm_name") and edit the ```group_vars/project/main.yml``` (sometimes ```group_vars/project/common.yml``` or ```/roles/project/vars/main.yml``` to have php_version: "8.1")

## Run the Ansible playbook

- Run the ```playbooks/[project_name].yml``

## Deploy the application 

- Run a cap to deploy the application
- See documentation in application repos for specific deploy instructions


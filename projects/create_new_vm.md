
### Create a new Jammy VM

## Create the Host on VSphere

- Search for "template" and open template_jammy_2023_11
- Click on Actions and then New VM from This Template
    1. Name is same as original VM
    2. On dev, choose Library-Dev -> Discovered virtual machine
    3. On prod, choose Library -> dev-rebootable-vms
    4. Put on same compute resource as original VM
    5. Put on storage that has the most space free
    6. From "Select virtual disk format" drop-down, choose Thin Provision
    7. Check boxes: "Customize this virtual machine's hardware" and "Power on virtual machine after creation"
    8. Choose client device from CD/DVD drive 1 drop-down
    9. Expand Network adapter 1, from the automatic MAC address copy and paste all the  characters. (You will need these for the network registration below)

## Register the Host on the Network

1. Our network requires a registered MAC address to OIT DHCP Server. The steps to do this can be accomplished at the following [URI Network registration](https://princeton.service-now.com/service?id=sc_category&sys_id=0c0591f14f9d270c18ddd48e5210c79c) - use the MAC address from step 9 above
2. Put in an l-support ticket to open firewall rules (assign to yourself)
3. Log into Palo Alto and [create rules](https://github.com/pulibrary/pul-it-handbook/blob/main/services/panos_fw.md)
4. Respond to the ticket with details of the rules and close ticket (see [examples](https://github.com/pulibrary/ops-catchall/blob/main/projects/panos_editing.md) of ServiceNow ticket wording)


## Prepare your New VM

- Log in to new VM
-  - Because this is the first time logging into the VM, you may get a warning about a man-in-the-middle attack, and you will need to edit the .ssh/known_hosts file to add your ssh keys at the line specified in the output you receive. Shortcut command to do this: ```vim ~/.ssh/known_hosts +"[line#]d|x"```
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

## Add your host to the prancible inventory

Create a new branch and make a PR to add VM to princeton_ansible/inventory/all_projects/[project_name]

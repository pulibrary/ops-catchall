
### Create a new Jammy VM

## Create the Host on VSphere

- Search for "template" and open the latest Jammy Jellyfish template (e.g. "jammy_season_year")
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
    - In the Device Type drop-down, start typing in Linux and choose Linux Server
    - In the Model drop-down, start typing VMWare and choose VMWare, Inc. VMWare Virtual Platform
    - For Ownership type, choose University 
    - For "What would you like to register?" choose Wired from the drop-down
    - For Type of wired IP, choose Static 
    - For Network/Subnet choose, start typing libnet and choose that from the drop-down
    - Under wired MAC address, paste the MAC address that you copied in step 9 in the previous section. Click the small "Add" button underneath
    - Under Options as the last step, choose "Add external view if IPs globally routable"
    - Submit the form; you should then receive IP address and other information in your email
    
2. Put in an l-support ticket to open firewall rules (assign to yourself)
3. Log into Palo Alto and [create rules](https://github.com/pulibrary/pul-it-handbook/blob/main/services/panos_fw.md)
4. Respond to the ticket with details of the rules and close ticket (see [examples](https://github.com/pulibrary/ops-catchall/blob/main/projects/panos_editing.md) of ServiceNow ticket wording)


## Prepare your New VM

- Log in to new VM
-  - Because this is the first time logging into the VM, if another VM has had the same FQDN (for example, `my-project-staging1`) in the past, you will get a warning about a man-in-the-middle attack. If you get this warning, you need to edit your local `.ssh/known_hosts` file to remove the old entry for that IP. The error output specifies the line number to look for and delete. Shortcut command to do this: ```vim ~/.ssh/known_hosts +"[line#]d|x"```
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

## Add your host to the prancible inventory

Create a new branch and make a PR to add VM to princeton_ansible/inventory/all_projects/[project_name]

## Add users to your VM

On our Jammy templates, the pulsys user is added automatically (for non-Operations folks, you will need to run the pulsys playbook to add other IT members as pulsys). There are some cases where you may want to add users other than pulsys to be able to SSH into your VM. To do that, follow these steps: 

1. Sign in to your VM as the pulsys user, i.e. 
```ssh pulsys@sandbox-vkarasic.lib.princeton.edu```

2. Replace username with the name of the user you are adding: ```sudo useradd -s /usr/bin/bash -d /home/username -m -G sudo username```

3. ```sudo su - username```

4. Copy the URL for your GitHub keys and enter this command, pasting in the URL you just copied: 
```wget https://github.com/YourUsername.keys```

5. Make a new directory for the new .ssh keys and change the permissions so that the owner can read, write, and execute: 
```mkdir .ssh```
```chmod 700 .ssh```

6. Create a new file in your directory for authorized_keys, and then put your keys in that file: 
```touch .ssh/authorized_keys```
```cat YourUsername.keys >> .ssh/authorized_keys```

7. Change the permissions on the authorized_keys file so that the owner can read and write: 
```chmod 600 .ssh/authorized_keys```

8. Add the new user into the sshd_config file as an allowed user (in the config file, AllowUsers should be around line 123):
```sudo vim /etc/ssh/sshd_config```

9. Restart sshd for your changes to take effect: 
```sudo systemctl restart sshd```

10. Exit your sandbox as the pulsys user and ssh back in as the new user name you just added. 

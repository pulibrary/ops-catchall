
### Create a new Jammy VM

## Create the Host on VSphere

- Search for "template" and open the latest Jammy Jellyfish template (e.g. "jammy_season_year")
- Click on Actions and then New VM from This Template
    1. If replacing a VM, name is same as original VM. If creating a new VM, enter the FQDN (fully qualified domain name) indicated on the ticket or by the requester. 
    2. On dev, choose Library-Dev -> Discovered virtual machine
    3. On prod, choose Library-Dev -> AnsibleBuiltVMs
    4. Choose a compute resource* (any machine that does not currently have an issue notification on it will be fine).* 
    5. Put on storage that has the most space free
    6. From "Select virtual disk format" drop-down, choose Thin Provision
    7. Check boxes: "Customize this virtual machine's hardware" and "Power on virtual machine after creation"
    8. Expand "Network adapter 1," and from the automatic MAC address, copy and paste all the characters. (You will need these for the network registration below)
    *Note: Machines with "a" next to the name are in Forrestal; machines with "b" next to the name are in New South.

## Register the Host on the Network

1. Our network requires a registered MAC address to OIT DHCP Server. The steps to do this can be accomplished at the following [URI Network registration](https://princeton.service-now.com/service?id=sc_category&sys_id=0c0591f14f9d270c18ddd48e5210c79c) - use the MAC address from step 9 above
    - In the Device Type drop-down, start typing in Linux and choose Linux Server
    - In the Model drop-down, start typing VMWare and choose VMWare, Inc. VMWare Virtual Platform
    - For Ownership type, choose University 
    - For "What would you like to register?" choose Wired from the drop-down
    - For Type of wired IP, choose Static 
    - If not on the private network: For Network/Subnet choose, start typing libnet and choose that from the drop-down
    - If on the private network: For Network/Subnet choose, start typing ip4-library and choose ip4-library-servers from the drop-down
    - Under wired MAC address, paste the MAC address that you copied in step 9 in the previous section. Click the small "Add" button underneath
    - If not on the private IP network: Under Options as the last step, choose "Add external view if IPs globally routable." If on the private IP network, do not select any boxes here. 
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

Our VMS will be using 14GB or the allocated thin provisioned 28GB. Run the [Expand Disk playbook](https://ansible-tower.princeton.edu/#/templates/job_template/61/) from Ansible Tower to utilize all of the disk space.

## Add your host to the prancible inventory

Create a new branch and make a PR to add VM to princeton_ansible/inventory/all_projects/[project_name]

## Add users to your VM

On our Jammy templates, keys for the Operations team are automatically added to the pulsys user's authorized_keys file. Non-Operations folks must run the [update pulsys user keys playbook](https://ansible-tower.princeton.edu/#/templates/job_template/17/details) to add their keys and enable SSH access as the pulsys user. There are some cases where you may want to SSH into a VM as a user other than pulsys. To add a different user, follow these steps: 

1. Sign in to your VM as the pulsys user, i.e. 
- ```ssh pulsys@sandbox-yoursandbox.lib.princeton.edu```

2. Create the user. Replace "username" in the following command with the name of the user you are adding: 
- ```sudo useradd -s /usr/bin/bash -d /home/username -m -G sudo username```

3. Switch to the new user. Again, replace "username" in the following command with the name of the user you are adding: 
- ```sudo su - username```

4. Enter the URL for your GitHub public keys through this command (in the following URL, replace "YourUsername" with your GitHub username; the rest of the URL should remain the same):

- ```wget https://github.com/GitHubUsername.keys```

5. Make a new directory for the new .ssh keys and change the permissions so that the owner can read, write, and execute: 
- ```mkdir .ssh```
- ```chmod 700 .ssh```

6. Create a new file in your directory for authorized_keys, and then put your keys in that file: 
- ```touch .ssh/authorized_keys```
- ```cat GitHubUsername.keys >> .ssh/authorized_keys```

7. Change the permissions on the authorized_keys file so that the owner can read and write: 
- ```chmod 600 .ssh/authorized_keys```

8. Edit the sshd_config file to add the new user as an allowed user (in the config file, AllowUsers should be around line 123):
- ```sudo vim /etc/ssh/sshd_config```

9. After saving your changes to the config file, restart sshd for your changes to take effect: 
- ```sudo systemctl restart sshd```

10. Exit your session as the new user, then exit your SSH connection as the pulsys user, then ssh back in as the new user you just added. 

### On Mondays, run the os_updates playbook on staging machines and the security_theater playbook on qa and production machines.

#### We prefer to run these updates on Ansible Tower so that we have a shared record of results

##### To run os_updates on staging: 

1. Open [Ansible Tower](https://ansible-tower.princeton.edu/) and click Templates in the left-hand navigation menu. Search for "Os Updates" in the search box and click on that template called "OS Updates Patch." 
2. Click the Launch button at the bottom to choose launch options. Type in "main" for Source Control Branch, and type in "staging" for Limit. Verbosity can stay as the default at "0 (Normal)." Click Next. 
3. Choose "staging" from which environment you want to run. For "how many vms do you want to run at once?" any number between 10 and 15 works well. Click Next. 
4. Click Launch. You can then click the Jobs tab in the left-hand navigation to make sure the job has launched and is running. 

#### In case you need to do this via the command line, follow these steps: 
1. Open a Terminal window to princeton_ansible repo
2. Log in to VPN and lastpass
3. Run the following playbook: 
`ansible-playbook playbooks/os_updates.yml`

4. Open a new Terminal window 
5. Run `caffeinate -d -t 10800` to keep your session active for 3 hours (this is an example; can set any time you'd like)

Notes: Some machines will fail for now
- Anything with pulcloud.io
- Machines still on Bionic and not upgraded to Jammy
- Any client that connects to mysql and postgresql

### On Tuesdays, run the os_updates playbook on production machines 

1. Repeat steps 1 & 2 from Mondays.
2. Run the following playbook on production machines: 
`ansible-playbook playbooks/os_updates.yml -e runtime_env=production`
3. Steps 4 & 5 and notes are same as those on Mondays. 

### On Tuesdays, run the os_updates playbook on qa machines 

1. Repeat steps 1 & 2 from Mondays.
2. Run the following playbook on qa machines: 
`ansible-playbook playbooks/os_updates.yml -e runtime_env=qa`
3. Steps 4 & 5 and notes are same as those on Mondays. 

### Troubleshooting

1. Some updates will require the endpoint to reboot. The [Dataspace](https://dataspace.princeton.edu) application will sometimes fail after reboot. The site will return with an error saying it is in maintenance mode. You will need to run the following steps to get it back up.

  ```bash
  ssh -J pulsys@bastion-prod.pulcloud.io pulsys@10.244.0.2
  sudo su - dspace
  dsbounce
  ```

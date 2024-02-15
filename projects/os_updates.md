### On Mondays, run the os_updates playbook on staging machines

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
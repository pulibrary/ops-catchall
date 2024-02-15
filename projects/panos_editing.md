## Creating and modifying firewall rules

You must complete two tasks for each firewall change (edit or addition):
1. Create or update the Service Portal ticket
2. Create or update the firewall rule(s) in PanOS

Use our standard firewall rule format in both the Service Portal and in PanOS. The rule names should incorporate the Object names from PanOS. The format for naming a firewall rule is:
`<DataCenter>_<ObjectName>_Rule#`

For example: `Shared_PulfaLight-Prod1_Rule1` or `Forrestal_lib-serv79_Rule1` - 

### Creating or updating the Service Portal ticket:

Service Portal tickets document firewall changes over time and provide an audit history. You must create or update a Service Portal ticket for each firewall change you make. To create a ticket in the Princeton Service Portal, you can review past jkazmier@ tickets with "Hardware Firewall Change - hostname.princeton.edu" and copy and paste and adjust accordingly.

Example Ticket Template:
(Note: this example shows multiple change types. Most real tickets will have only one change type.)

```
Subject: Hardware Firewall Change – <Lib-ServerName>
Description: <Content of email from requestor, or the reason for the change.>

Resolution Format: 

Rules:

Rule "<DataCenter>_<Lib-ServerName>_Rule#" created:
Source: Any
Destination: <Lib-ServerName>
Application: <application> (for example, ssl, web-browsing)
Ports: HTTP (TCP 80), HTTPS (TCP 443)
Action: Allow

Rule "<DataCenter>_<Lib-ServerName>_Rule#" edited:
Removed Source: Princeton Wired
Added Source: Any
Removed Application: <application> (for example, ssl, web-browsing)
Added Ports: HTTP (TCP 80), HTTPS (TCP 443)

Rule "<DataCenter>_<Lib-ServerName>_Rule#" deleted:
Reason for deletion: Rule was no longer needed or server has been decommissioned or redundant to rule xyz
Was configured as:
Location: Forrestal
Source: Any
Destination: Lib-ServerName
Application: ssl, web-browsing
Ports: HTTP (TCP 80), HTTPS (TCP 443)
Action: Allow

Rule "Rule ID 250" renamed: "Shared_DSS_Rule6"
Rule "Rule ID 250" moved to device group: “Shared”

Addresses:

Address “Lib-ServerName” created:
Name: Lib-ServerName
Shared: Yes
Description: Aliases or See Ticket# ABC123456
IP Address: 128.112.###.###/32

Address “Lib-ServerName” edited:
Original IP Address: 123.123.123.123
New IP Address: 789.789.789.789

Address “Lib-ServerName” deleted:
Reason for deletion: Ex: The archaic piece of junk finally died. Server has been replaced by lib-ShinyNewServer.
Was configured as:
Name: Lib-ServerName
Shared: Yes
Description: Aliases
IP Address: 128.112.###.###/32

Services:

Service “ServiceName” created:
Name: ServiceName
Description: See Ticket # ABC123456
Shared: Yes
Protocol: TCP (or UDP)
Destination Port: #####
Source Port: 0-65535
Service “ServiceName” deleted:
Reason for deletion:
Was configured as:
Name: ServiceName
Description: See Ticket # ABC123456
Shared: Yes
Protocol: TCP (or UDP)
Destination Port: #####
Source Port: 0-65535
Service “ServiceName” renamed: “NewServiceName”

Security Profiles:

Anti-Spyware Security Profile "AS-ResetC-AlertHML" created:
Location: Shared
DNS Action: alert
DNS Packet Capture: disable
Rule Name: simple-critical
- Threat Name : any
- Severity: critical
- Action: reset-both
- Packet Capture: disable
Rule Name: simple-high
- Threat Name : any
- Severity: high
- Action: alert
- Packet Capture: disable
Rule Name: simple-medium
- Threat Name : any
- Severity: medium
- Action: alert
- Packet Capture: disable
Rule Name: simple-low
- Threat Name : any
- Severity: low
- Action: alert
- Packet Capture: disable

Vulnerability Protection Security Profile "Vuln-ResetC-AlertHM" created:
Location: Shared
Rule Name: simple-client-critical
- Threat Name : any
- Host Type: client
- Severity: critical
- Action: reset-both
- Packet Capture: disable
Rule Name: simple-client-high
- Threat Name : any
- Host Type: client
- Severity: high
- Action: alert
- Packet Capture: disable
Rule Name: simple-client-medium
- Threat Name : any
- Host Type: client
- Severity: medium
- Action: alert
- Packet Capture: disable
Rule Name: simple-server-critical
- Threat Name : any
- Host Type: server
- Severity: critical
- Action: reset-both
- Packet Capture: disable
Rule Name: simple-server-high
- Threat Name : any
- Host Type: server
- Severity: high
- Action: alert
- Packet Capture: disable
Rule Name: simple-server-medium
- Threat Name : any
- Host Type: server
- Severity: medium
- Action: alert
- Packet Capture: disable

Security Profile Group "SP-Alert-Only" renamed "SP-ResetC-AlertHML"

Security Profile Group "SP-Alert-Only" edited:
Changed Anti-Spyware Profile: AS-ResetC-AlertHML
Changed Vuln Protection Profile: Vuln-ResetC-AlertHM

External Dynamic Lists

External Dynamic List “Some Block List” created:
Name: Some Block List
Shared: Yes
Description: URL to page describing the block list.
Source: URL to text file containing list of IPs
Frequency: Weekly on Monday at 00:00
```

### Creating the firewall rule in PanOS:
Creating or modifying firewall rules is a three-step process: First, create or modify the appropriate rule or rules. Second, commit the changes. Third, push the committed changes out to all devices.

1. Create or modify the rule(s):
* If you are adding a new host, create a new firewall object with the IP address. Skip this step if you are modifying an existing rule for an existing host.
  * **On the Objects Tab** 
    * Click `Add` (plus sign) at bottom left
	  * Enter vm name, click `Shared`
    * Add a description
	  * Keep the default Type, and add the IP address in the blank box to the right
    * Click OK to save
* If you are adding new rules (for a new host or for an existing host), create a new rule record. Skip this step if you are modifying an existing rule for an existing host.
  * **On the Policies tab**
    * The Device Group should always be set to Shared for VMs
    * Highlight the rule you want to clone, then click `Clone` in the bottom toolbar
    * In the popup, select the rule, click `OK` to clone - your cloned rule will show up as `Original-Rule#-1`
    * Click on your new rule name to open the edit box
    * Edit the name of your new rule on the General tab
* Edit the cloned or pre-existing rule record.
  * Open the rule record and edit or check three tabs: Source, Destination, Application Service to match your needs
  * If the rule record is a clone, change the Destination
  * add application rules (see [applipedia](https://applipedia.paloaltonetworks.com/) for rule definitions)
  * Click OK to save

2. Commit the changes to Panorama:
  1.	In the upper right click the “Commit” link
  2.	In the Commit window, select the “Panorama” radio button
  3.	Click “Commit” to save changes to Panorama
  4.	After it is complete, click the “Commit” link once again
  5.	This time select the “Device Group” radio button
  6.	Select “Forrestal” and “New-South” 
  7.	Click “Commit” to save changes to each si

3. Push the changes to all devices:
* Push to Devices (make sure it has pushed before)

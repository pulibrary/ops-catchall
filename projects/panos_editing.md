#### Template below for Service Portal:

Note: You can also review past jkazmier@ tickets with "Hardware Firewall Change - hostname.princeton.edu" and copy and paste and adjust accordingly. Otherwise

```
Example Ticket Template:
Subject: Hardware Firewall Change – Lib-ServerName
Description: Content of email from requestor. Or simply the reason for the change.

Resolution Format: 

Rules:

Rule "Forrestal_Lib-ServerName_Rule#" created:
Source: Any
Destination: Lib-ServerName
Application: ssl, web-browsing
Ports: HTTP (TCP 80), HTTPS (TCP 443)
Action: Allow

Rule "Forrestal_Lib-ServerName_Rule#" edited:
Removed Source: Princeton Wired
Added Source: Any
Removed Application: ssl, web-browsing
Added Ports: HTTP (TCP 80), HTTPS (TCP 443)

Rule "Forrestal_Lib-ServerName_Rule#" deleted:
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

Committing the changes:
After making the appropriate changes to the FW, they must be committed to go into effect. 
1.	In the upper right click the “Commit” link
2.	In the Commit window, select the “Panorama” radio button
3.	Click “Commit” to save changes to Panorama
4.	After it is complete, click the “Commit” link once again
5.	This time select the “Device Group” radio button
6.	Select “Forrestal” and “New-South” 
7.	Click “Commit” to save changes to each si
```



## Firewall Rule Modification Steps

* Create IP address of host
* Objects Tab 
    * Add (plus sign) # bottom left
	  * get name and add to shared
	  * add IP address 
* Open policies tab
    * Shared Policies (rules are sequential)
	* Highlight Rule and Clone
	* give the name to new policy
	* In order of importance (Source, Destination, Application Service)
	* Change to new destination if cloned
	* add application rules (applipedia for rules definition)
	
Upon Completion DO

* Commit to Panorama
* Push to Devices (make sure it has pushed before)

	
	

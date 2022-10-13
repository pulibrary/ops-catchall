### Confluence LDAP Settings

The following settings are needed to connect to the OIT provided Active Directory LDAP. 

#### Server Settings

  * Name: `Descriptive Name`
  * Directory Type: `Microsoft Active Directory` # from drop down
  * Hostname: `ldapproxy.princeton.edu`
  * Port: `636` # Check Use SSL
  * Username: `CN=<username>,OU=Library - Information Technology,OU=People,DC=pu,DC=win,DC=princeton,DC=edu`
  * Password: `FromLastPass`
  
#### LDAP Schema

  * Base DN: `dc=pu,dc=win,dc=princeton,dc=edu`
  * Additional User DN: `[Leave Blank]`
  * Additional Group DN: `[Leave Blank]`
  
#### LDAP Permissions

  * Read Only, with Local Groups # Use Radio Button
  * Default Group Memberships: `confluence-users`

#### Advanced Settings

  * Secure SSL # Check Box
  * Filter out expired users # Check Box
  * Use Paged Results `1000`  results per page # Check Box
  * Follow Referrals # Check Box
  * Naive DN Matching # Check Box
  * Enable Incremental Synchronization
  * Synchronization Interval (minutes): `600`
  * Read Timeout (seconds): `120`
  * Search Timeout (seconds): `300`
  * Connection Timeout (seconds): `150`

#### User Schema Settings

  * User Object Class: `user`
  * User Object Filter: `(&(objectCategory=Person)(sAMAccountName=*))`
  * User Name Attribute: `sAMAccountName`
  * User Name RDN Attribute: `cn`
  * User First Name Attribute: `givenName`
  * User Last Name Attribute: `sn`
  * User Display Name Attribute: `displayName`
  * User Email Attribute: `mail`
  * User Password Attribute: `unicodePwd`
  * User Unique ID Attribute: `objectGUID`

#### Group Schema Settings

  * Group Object Class: `group`
  * Group Object Filter: `(objectCategory=Group)`
  * Group Name Attribute: `cn`
  * Group Description Attribute: `description`

#### Membership Schema Settings

  * Group Members Attribute: `member`
  * User Membership Attribute: `memberOf`
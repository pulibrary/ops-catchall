
### Posh-ACME with IIS

This guide provides instructions on the [Posh-ACME utility](https://poshac.me/docs/v4/) with your windows server. Posh-ACME strives to have feature parity with Certbot thus dramatically reduces the effort (and cost) of securing your websites with HTTPS. It works directly with the free ACME protocol to request (or renew) a certificate, prove ownership of the domain, and install the InCommon provided certificate on your web server.
#### Before you begin

Before continuing with this guide, you need a website accessible over HTTP using your desired domain name. Breaking this down further, the following components are required:

A Windows Server with credentials to a standard user account (belonging to the admin group) and the ability to access the server.

A registered domain name with DNS records pointing to the IPv4 (and optionally IPv6) address of your server. A domain name can be obtained through Princeton's OIT. 

#### Create the PowerShell Script


Install Posh-ACME:

```bash
Install-Module -Name Posh-ACME -Scope CurrentUser

```

The following script will match what sectigo's API expects from all requests:

```bash
# Import Posh-ACME module
Import-Module Posh-ACME

# Define your variables
$Email = "support@princeton.edu"
$EabKid = "your-eab-kid-value"
$EabHmacKey = "your-eab-hmac-key-value"
$Domains = @("example.edu", "www.example.edu") # Replace with your domains
$CertName = "example-cert-name"

# Set up the Posh-ACME account with EAB
New-PAAccount -Email $Email -AcceptTOS -EABKeyID $EabKid -EABHmacKey $EabHmacKey -Server "https://acme.sectigo.com/v2/InCommonRSAOV"

# Request the certificate
New-PACertificate -Domain $Domains -CertName $CertName -Install

```

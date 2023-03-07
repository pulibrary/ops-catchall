### Certbot Ubuntu with webservers

This guide provides instructions on the Certbot utility with your web server on Ubuntu 22.04 LTS. Certbot dramatically reduces the effort (and cost) of securing your websites with HTTPS. It works directly with the free ACME protocol to request (or renew) a certificate, prove ownership of the domain, and install the InCommon provided certificate on your web server. Consider using the [playbook](https://github.com/pulibrary/princeton_ansible/blob/main/playbooks/incommon_certbot.yml) given how many steps are involved :wink:

#### Before you begin

Before continuing with this guide, you need a website accessible over HTTP using your desired domain name. Breaking this down further, the following components are required:

A server running on Ubuntu 22.04 LTS with credentials to a standard user account (belonging to the sudo group) and the ability to access the server through SSH.

A registered domain name with DNS records pointing to the IPv4 (and optionally IPv6) address of your server. A domain name can be obtained through Princeton's OIT. 

The web server software installed on your server and configured for your domain.

#### Configure your firewall

Any firewall configured on your server needs to allow connections over HTTPS (in addition to HTTP and any other services/ports you require). This section covers enabling and configuring UFW (UncomplicatedFirewall). UFW is the default firewall management tool on Ubuntu. It operates as a easy to use front-end for iptables.

Depending on your network you may need to work with Princeton's OIT

If UFW is not installed, install it now using apt.

```zsh
sudo apt -y update
sudo apt -y install ufw
```

Add firewall rules to allow ssh (port 22) connections as well as http (port 80) and https (port 443) traffic.

```zsh
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
```

Your server may require additional rules depending on which applications you’re running (such as mail servers or database servers) and if those applications need to be accessible from other systems.

Enable UFW if its not already enabled.

```zsh
sudo ufw enable
```

Verify that UFW is enabled and properly configured for ssh and web traffic.

```zsh
sudo ufw status
```

This should return a status of active and output the firewall rules that you just added.

#### Installing Snapd

[Snap](https://snapcraft.io/about) is a package manager developed by Canonical (creators of Ubuntu). Software is packaged as a snap (self-contained application and dependencies) and the snapd tool is used to manage these packages. Since certbot is packaged as a snap, we’ll need to install snapd before installing certbot.

If snapd is not installed, install it now.

```zsh
sudo apt update
sudo apt install snapd
```

Install the *core* snap.

```zsh
sudo snap install core
sudo snap refresh core
```

#### Installing Certbot

The next step is to install Certbot using the snap command.

Remove any previously installed certbot packages to avoid conflicts with the new Snap package.

```zsh
sudo apt remove certbot
```

Use Snap to install Certbot.

```zsh
sudo snap install --classic certbot
```

Configure a symbolic link to the Certbot directory using the ln command.

```zsh
sudo ln -sf /snap/bin/certbot /usr/bin/certbot
```

#### Request a TLS Certificate with Certbot

You will need a working email address. Run the following commands with the eab-kid and eab-hmac-key provided by working with Princeton's OIT. In the example below we use

eab-kid value `XXXxxNNxNNWxXxxXNXxXx` and eab-hmac-key value of `_XNXXxxNXNNXxXNXXNXXXxxxXNNxXxxXXXxxXXXNX_XXxXxNxNNNX_XXxxNXXxXNxxXXXXxXxxXXXxXxNzXXxX`

for the vanity-name.princeton.edu domain

```zsh
sudo certbot certonly --standalone --non-interactive --agree-tos --email netid@princeton.edu --server https://acme.sectigo.com/v2/InCommonRSAOV --eab-kid XXXxxNNxNNWxXxxXNXxXx --eab-hmac-key _XNXXxxNXNNXxXNXXNXXXxxxXNNxXxxXXXxxXXXNX_XXxXxNxNNNX_XXxxNXXxXNxxXXXXxXxxXXXxXxNzXXxX  --domain vanity-name.princeton.edu --cert-name vanity-name
```

Your TLS pem files will be saved under

```zsh
root@lib-adc2:/home/username# ls -al /etc/letsencrypt/live/vanity-name/
total 12
drwxr-xr-x 2 root root 4096 Mar  7 18:30 .
drwxr-xr-x 6 root root 4096 Mar  7 18:30 ..
lrwxrwxrwx 1 root root   40 Mar  7 18:30 cert.pem -> ../../archive/vanity-name/cert1.pem
lrwxrwxrwx 1 root root   41 Mar  7 18:30 chain.pem -> ../../archive/vanity-name/chain1.pem
lrwxrwxrwx 1 root root   45 Mar  7 18:30 fullchain.pem -> ../../archive/vanity-name/fullchain1.pem
lrwxrwxrwx 1 root root   43 Mar  7 18:30 privkey.pem -> ../../archive/vanity-name/privkey1.pem
-rw-r--r-- 1 root root  692 Mar  7 18:30 README
```

Configure your Webserver to use the files above.

#### Renewing TLS Certificates

Upon installation, Certbot is configured to renew any certificates automatically. It is not necessary to manually request an updated certificate or run Certbot again unless the site configuration changes. However, Certbot makes it possible to test the auto-renew mechanism or to forcibly update all certificates.

#### Test automated renewals

To confirm Certbot is configured to renew its certificates automatically, use certbot renew along with the dry-run flag.

```zsh
sudo certbot renew --dry-run
```

Certbot inspects the certificates and confirms they are not due to be renewed, but simulates the process anyway. It displays details regarding whether the renewal would have been successful.
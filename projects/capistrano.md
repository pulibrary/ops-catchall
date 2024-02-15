### Using Capistrano to deploy a Rails project

## Prerequisites

  1. Create a Deploy User
  2. Set up [Passenger](https://www.phusionpassenger.com/library/config/nginx/intro.html) for Nginx
  3. Push our application to [Rails Bridge](rails_bridge.md) application to GitHub

### Create a Deploy User

Login to your sandbox

```zsh
ssh pulsys@sandbox-<yournetid>.lib.princeton.edu
```

Approximate expected result:

```
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Thu Oct  6 16:34:27 2022 from 172.20.202.43
```
The resulting text may differ. This is the version in fall of 2022

Create the deploy user with the following commands:

```zsh
sudo bash
adduser conan
```
When prompted to create a new password for the user, please note that what you enter will not echo back what you type.

Approximate expected result:

```
❯ sudo bash
root@sandbox-<yournetid>:/home/pulsys# adduser conan
Adding user `conan' ...
Adding new group `conan' (1001) ...
Adding new user `conan' (1001) with group `conan' ...
Creating home directory `/home/conan' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for conan
Enter the new value, or press ENTER for the default
	Full Name []: Conan TheDeployer
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y
```

Edit your ssh configuration file at `/etc/ssh/sshd_config` to allow the deploy user access to your sandbox

```zsh
sudo vi /etc/ssh/sshd_config
```

search for pulsys on the file and add the new username `conan` to the list of allowed users

Approximate expected result:

```
...
...
...
# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
UseDNS no
GSSAPIAuthentication no
PermitRootLogin no
PasswordAuthentication no
LoginGraceTime 20
AllowUsers pulsys conan
```


#### Login as the New User

Exit from your sandbox. Copy your local sshkeys to the sandbox with the following steps:

```zsh
cat ~/.ssh/id_ecdsa.pub
```

Approximate expected result:

```
ecdsa-sha2-nistp256 tytyyiy776fnnsjjsjfhggalzdHAyNTYAAABBBKRnDiuSOBsUFmv5II+K+DekV6NQGgv+DWL94IC8QY6LNa3x57dhhsallspwTXvJrIYx5DtHiCUaSKaGUYjgXww= username@laptop.local
```

Copy the contents of the results above into your clipboard:

Log back into your sandbox as the pulsys user then switch to become the conan user with the following steps:

```zsh
ssh pulsys@sandbox-<netid>.lib.princeton.edu
sudo su - conan
```

Approximate expected result:

```
❯ ssh pulsys@sandbox-<netid>.lib.princeton.edu
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Fri Oct  7 11:30:31 2022 from 172.20.202.43
❯ sudo su - conan
conan@sandbox-<netid>:~$
```

Paste the contents of your clipboard into a new file `/home/conan/.ssh/authorized_keys` 

```zsh
mkdir /home/conan/.ssh
vim /home/conan/.ssh/authorized_keys
```

Exit as the conan user (this will make you become the `pulsys` user). Restart the ssh service with the following command:

```zsh
sudo systemctl restart ssh
```

then exit the sandbox.

You should now be able to log in as the `conan` user with:

```zsh
ssh conan@sandbox-<yournetid>.lib.princeton.edu
```

Approximate expected result:

```
❯ ssh conan@sandbox-<yournetid>.lib.princeton.edu
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

conan@sandbox-<yournetid>:~$
```

exit as the conan user

### Set up Passenger

These commands will install Passenger + Nginx module through Phusion's APT repository.

```zsh
sudo apt-get install -y dirmngr gnupg
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates


sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger jammy main > /etc/apt/sources.list.d/passenger.list'
sudo apt-get update


sudo apt-get install -y libnginx-mod-http-passenger
```

Ensure the config files are in-place:

```zsh
if [ ! -f /etc/nginx/modules-enabled/50-mod-http-passenger.conf ]; then sudo ln -s /usr/share/nginx/modules-available/mod-http-passenger.load /etc/nginx/modules-enabled/50-mod-http-passenger.conf ; fi
sudo ls /etc/nginx/conf.d/mod-http-passenger.conf
```

If you don't see a file at /etc/nginx/conf.d/mod-http-passenger.conf; then you need to create it yourself and set the passenger_ruby and passenger_root config options. For example:

```zsh
passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini;
passenger_ruby /usr/bin/passenger_free_ruby;
```

When you are finished with this step, restart Nginx:

```zsh
sudo systemctl restart nginx
```

check installation

```zsh
sudo /usr/bin/passenger-config validate-install
```

### Deploy A Rails App

Type this in the terminal:

```zsh
cd ~/railsbridge/test_app
```
Add the Capistrano gem in the development section of your Gemfile:

```ruby
group :development do
  # Deployment
  gem "capistrano", "~> 3.10", require: false
  gem "capistrano-rails", "~> 1.3", require: false
end
```

Type this in the terminal:

```zsh
cd ~/railsbridge/test_app
git init
```

Expected Result.

```
Initialized empty Git repository in ~/railsbridge/test_app/.git/
```

Type this in the terminal:

```zsh
git add -A
```

Type this in the terminal:

```zsh
git commit -m "initial commit"
```

Expected result

```
a lot of lines like create mode 100644 Gemfile
```

Type this in the terminal:

```zsh
git log
```

Expected result

```
(Your git name and "initial commit" message.)
```

You need the capistrano-rails gem to run a migration on your database and handle the assets that we will see in the next sections.

Now run bundle install to add Capistrano to your application:

```zsh
bundle install
```

Then you can install Capistrano:

```zsh
bundle exec cap install
```

This command will create a file called Capfile that imports and configures the required libraries. Furthermore, a file called deploy.rb (created inside the config folder) will contain all the configurations that should be included in a deployment with Capistrano.

Note: Capistrano creates a file to override the deploy.rb configuration for both the staging and production environments, but that will not be covered in the tutorial.

With that, you're ready to start configuring and communicating between your Ruby on Rails application and the server.
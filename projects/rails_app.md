## Install Refinery CMS with Nginx on Ubuntu

### Installation Prerequisites

In order to set the Rails App you will need

  * A working version of Ruby
  * [PostgreSQL](postgresql.md) database configured

#### Ruby/Rubygems

This is not a production server you are working on so we recommend install Ruby via [rbenv](https://github.com/rbenv/rbenv) which allows one to install more than one version of Ruby or RubyGems. 

This is done using the following commands:

```bash
sudo apt-get install -y libssl-dev libsqlite3-dev nodejs
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
cd ~/.rbenv && src/configure && make -C src
```

If you are using the *bash* shell add this to your config

```bash
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
```

Otherwise if you are using *zsh* add this to your config

```bash
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
```

Setup your rbenv in your shell

```bash
~/.rbenv/bin/rbenv init
```

Install ruby-build with the following commands:

```bash
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```
Check for the versions of ruby available to install 

```bash
rbenv install -l
```

We will install version 3.1.2 (the newest version in Fall 2022)

```bash
rbenv install 3.1.2
```


### Install Rails App

Set the global ruby version by running the following command:

```bash
rbenv global 3.1.2
```
Build a new Rails app

```bash
gem install rails
```

Create a directory for your application 

```bash
sudo mkdir /opt/apps
sudo chown -R $USER /opt/apps
```

Create you blog application

```bash
cd /opt/apps
rails new blog
```

Switch into the newly created blog directory

```bash
cd blog
rails server
```
You can now view your rails application by creating a *tunnel* connection from a different termimal window, and running the following commands:

```bash
ssh -L 3000:localhost:3000 pulsys@sandbox-<yournetid>
```
Ask any of the PUL developers or read more about [Rails](https://edgeguides.rubyonrails.org/getting_started.html) here. 
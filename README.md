# App Details

**IP address**
```
52.89.11.168
```

**App URL**
```
http://ec2-52-89-11-168.us-west-2.compute.amazonaws.com/
```

**Login**

_Place the provided Udacity private key file in your local_ `~/.ssh` _directory, and run:_

```
ssh -i ~/.ssh/udacity_key.rsa grader@52.89.11.168 -p 2200
```

_As a security precaution, log in as_ `root` _has been disabled. However,_ `grader` _has been given sudo access._


# Dependencies



# Server setup steps taken

_Here are the steps I personally took to complete this project. There are a lot of steps!_

#### Create an Amazon AWS virtual machine and get the private SSH key, VM IP address

```
https://www.udacity.com/account#!/development_environment
```

#### Log in as the default root user

```
$ ssh -i ~/.ssh/udacity_key.rsa root@52.89.11.168
```

## Update server 

#### View updatable packages

```
$ sudo apt-get update
```

#### Upgrade packages to latest available

```
$ sudo apt-get upgrade
```

#### If some downloads failed, try again

```
$ sudo apt-get upgrade --fix-missing
```

## Create new sudo-enabled user

#### Install "finger" app (not required but helpful for user management)

```
$ sudo sudo apt-get install finger
```

#### Create the new user, "grader"

```
$ sudo adduser grader
```

#### Add a sudoers file to "grader"

```
$ sudo nano /etc/sudoers.d/grader
```
_Add to this file:_

```
grader ALL=(ALL:ALL) ALL
```

#### Access and copy the public key already set up for user "root"

```
$ sudo cat /home/root/.ssh/authorized_keys
```

#### Paste this public key into the authorized_keys file for user "grader"

```
$ sudo nano /home/grader/.ssh/authorized_keys
```

#### Verify that you can now switch between these users successfully

```
$ su grader
$ su root
$ su grader
```

_Reset the password for the user "grader" and use a complex, secure password:_

```
$ passwd grader
```

_We've now given_ `grader` _sudo access. Now, be sure to stay logged in as_ `grader`

#### Remove the public key from the root user

_Delete all the contents in the authorized keys file for root:_

``` 
$ sudo nano /home/root/.ssh/authorized_keys
```
_The root user can no longer log in using the provided key pair. Let's now disable SSH access for root altogether in the following steps._


## Edit SSH configuration

#### Change SSH default port and disable root login

_Be sure to be logged in as_ `grader` _at this point._

```
$ sudo nano /etc/ssh/sshd_config
```

Change `Port 22` to `Port 2200`

Change `PermitRootLogin ...` to `PermitRootLogin no`

Change `PasswordAuthentication ...` to `PasswordAuthentication no`

_Restart sshd:_

```
$ sudo service ssh restart 
```

_This will restart the SSH service and apply our previous login changes. From now on, you will need to log in with:_

```
$ ssh -i ~/.ssh/udacity_key.rsa grader@52.89.11.168 -p 2200
```

## Edit ports and firewall

_Use the Uncomplicated Firewall plugin to configure a simple firewall._

#### Configure allowed connections:

```
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
```

``` 
$ sudo ufw allow ssh
$ sudo ufw allow 2200
$ sudo ufw allow 2200/tcp
$ sudo ufw allow 80
$ sudo ufw allow 80/tcp
$ sudo ufw allow 123
$ sudo ufw allow 123/tcp
```

#### Enable firewall

**WARNING! If the previous steps were not successful, enabling the firewall may prevent ALL user access to this server!**

_Verify that the aforementioned steps were successful. You can view all entered UFW rules using the following command:_

```
$ sudo grep '^### tuple' /lib/ufw/user*.rules
```

(Tip from [http://askubuntu.com/](http://askubuntu.com/questions/30781/see-configured-rules-even-when-inactive))

_When you are ready, activate the firewall and check its status:_

```
$ sudo ufw enable
$ sudo ufw status
```

## Set timezone to UTC

_Check the current timezone with the date command:
```
$ date
Mon Feb  1 12:35:12 UTC 2016
```

_If timezone is not_ `UTC`_, change it here in the timezone file:_

```
$ sudo nano /etc/timezone
```

## Install Apache HTTP Server and WSGI

#### Install Apache:

```
$ sudo apt-get install apache2
```
#### Install WSGI for Apache:

_Install WSGI, an Apache helper for Python applications. (For more info, see_ [mod_wsgi](http://www.modwsgi.org/)_)_

```
$ sudo apt-get install libapache2-mod-wsgi
```

#### Configure Apache to handle requests using the WSGI module

```
$ sudo nano /etc/apache2/sites-enabled/000-default.conf
```

_Append this line to the file, just before the closing_ `</VirtualHost>` _tag:_

```
WSGIScriptAlias / /var/www/html/myapp.wsgi
```

_This line will tell Apache to handle all URLs with the Python script_ `myapp.wsgi`_. If you would instead like to host this application at a different URL, you also could have entered:_

``` 
WSGIScriptAlias /myapp /var/www/html/myapp.wsgi
```

#### Restart Apache

```
$ sudo apache2ctl restart
```

## Install PostreSQL

```
$ sudo apt-get install postgresql
```

_Remote DB connections do not need to be allowed in this case. Since the database will also be hosted on this machine._

## Setup Git

#### Install

```
$ sudo apt-get install git-all
```
#### Configure a Git user

```
$ git config --global user.name "<name>"
```
#### Configure a Git email

```
$ git config --global user.email "<name>"
```


# Helpful Resources

[Udacity Getting Started Guide](https://docs.google.com/document/d/1J0gpbuSlcFa2IQScrTIqI6o3dice-9T7v8EDNjJDfUI/pub)

[Udacity Forums](https://discussions.udacity.com/t/linux-server-configuration-final-sql-alchemy-v-psql/44448)

[Digital Ocean - Initial Server Setup with Ubuntu 12.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-12-04)

[Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

[Error message when I run sudo: unable to resolve host (none)](http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none)
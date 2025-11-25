# Introduction
This is a guide to setup a new installation of WordPress + WP-CLI, running in an Ubuntu 24.04 Vagrant Box. Install Vagrant and Virtualbox before proceeding.

## Clone Repository

Clone Github repository to your local development machine:
```
git clone git@github.com:hamishau/vagrant.wordpress.git
```

## Project Name

Search for all instances of `yourapp` and replace with your preferred project name.

## Deploy Vagrant Box

```
vagrant up
```

## Edit Hosts

Windows Powershell: `notepad c:\Windows\System32\Drivers\etc\hosts`
Ubuntu Terminal: `sudo nano /etc/hosts`

```
192.168.56.10 yourapp.local
```

## Setup WordPress:

```
http://yourapp.local:8080
```

---

### Post Installation
These are not required as a part of the Vagrant deployment process - just a collection of helpful commands that might be required at some point in your Vagrant box.

#### Update WP-CLI
```
vagrant ssh -c 'sudo wp cli update'
```

#### Set Directory Permissions to 755:

```
vagrant ssh -c 'cd /var/www && find ./html -type d -exec chmod 0755 {} \;'
```

#### Set File Permissions to 644:

```
vagrant ssh -c 'cd /var/www && find ./html -type f -exec chmod 0644 {} \;'
```

#### Fix File and Folder Ownership:

```
vagrant ssh -c 'cd /var/www && sudo chown -R www-data:www-data ./html'
```

#### Export MySQL Database
```
vagrant ssh -c 'cd /var/www/html && sudo wp db export yourapp.sql --allow-root'
```

#### Import MySQL Database
```
vagrant ssh -c 'cd /var/www/html && sudo wp db import yourapp.sql --allow-root'
```

#### Reset WordPress Database
```
vagrant ssh -c 'cd /var/www/html && wp db reset --yes'
```

#### Restart Vagrant Box
```
vagrant reload
```

#### Destroy Vagrant Box
```
vagrant destroy
```

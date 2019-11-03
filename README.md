# Udacity-linux-server-config

This project aims to host a Flask application on a remote server through Amazon Lightsail. The application serves data using a PostgreSQL database, and has been configured to served a sports catalop application as a WSGI app.

## Server information

IP Address: 13.236.160.80

URL: http://13.236.160.80:80/

SSH Port: 2200

SSH Key: 

## Summary of software installed

This server is accessed using key-based SSH authentication through a Vagrant virtual machine. For this project, you must have Vagrant and Virtualbox set up on your machine. To install these, follow the instructions in the links below:

* [Vagrant](https://www.vagrantup.com/downloads.html)
* [Virtualbox](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)

## Summary of configurations made

## Get your server

I created an account and setup an Ubuntu server through Amazon Lightsail. So I could SSH into my instance on a different port to port 22, I created a 'Custom TCP' rule in the 'Networking' tab of my Lightsail instance homepage and set the port to 2200.

## Secure your server

I updated all packages by running `$ sudo apt-get update` followed by `$ sudo apt-get upgrade`

I then changed the SSH port from 22 to 2200 by changing line 13 in /etc/ssh/sshd_config using the command `$ sudo nano  /etc/ssh/sshd_config`. I uncommented the line 'Port 22' and changed it to 'Port 2200'.

I then configured by firewall by running the following series of commands:

```$ sudo ufw status```

Shows status of ufw firewall, made sure it was disabled.

```$ sudo ufw default allow outgoing```

Sets default to allow outgoing connections.

```$ sudo ufw default deny incoming```

Sets default to deny incoming connections.

```
$ sudo ufw allow ssh
$ ufw allow 2200/tcp 
$ sudo ufw allow 80
$ sudo ufw allow 123
```
These commands allow ssh connections, allow access to the SSH port that we configured the Virtual Machine to run on in the previous step, allow HTTP connections on port 80, and NTP on port 123.

I then ran `$ sudo ufw show added` to see all the rules I had set to ensure I had the correct setup before enabling the server.

Finally, I ran `$ sudo ufw enable` to start the firewall, and ran `$ sudo ufw status` again to check that the firewall was running. I saw that the status was 'active' and the rules I had configured were set up.

## Give `grader` SSH access and sudo permissions

Next, I created a user called grader using the command `$ sudo adduser grader` to allow the Udacity grader to log into my project.

I then gave grader sudo access by adding the line `grader ALL=(ALL) NOPASSWD:ALL` to the file /etc/sudoers.d/grader using the command `$ sudo nano /etc/sudoers.d/grader`

The next step was to setup SSH keypair login for the grader user. I created the keypair in my Vagrant virtual machine inside my vagrant user's home directory. To make these keys, I opened a new terminal window and logged into my Vagrant VM using `$ vagrant ssh`.

While logged into my Vagrant VM in my vagrant user's home directory, I created an .ssh directory with the command `$ mkdir .ssh`. I then made sure my vagrant user was the owner of the .ssh directory by running `$ chown vagrant:vagrant /home/vagrant/.ssh`, and changed the permissions of the file so only the vagrant user could write, view, and execute the directory with the command `$ chmod 700 /home/vagrant/.ssh`.

I then cd'ed into the '.ssh' directory and ran the command `$ ssh-keygen`, and named the file 'grader' when prompted to choose a file name to save the created keys into. This generated two files: 'grader', and 'grader.pub'. 

I then copied the contents of 'grader.pub' by running `$ sudo cat grader.pub' and copying the contents of the file.

I then switched back to my Lightsail instance terminal logged in as my grader user, and in my home directory /home/grader created a directory called .ssh and set the permissions to read read, write and execute for the grader user only with the following commands:

```
$ mkdir .ssh
$ chmod 700 .ssh/
```

I then cd'ed into my .ssh directory and made a file called 'authorized_keys', pasted the contents that I copied from my grader.pub file in my vagrant user's .ssh directory, and set the permissions to read only with the commands:

```
$ touch authorized_keys
$ chmod 400 authorized_keys
```

I then opened another terminal instance and logged into my Vagrant VM as my vagrant user, then logged into my Lightsail instance as my grader user using the newly created keypair with the following command:

```
ssh grader@13.236.160.80 -i /home/vagrant/.ssh/grader -p 2200
```

Finally, I disabled password login and remote root login by changing my '/etc/ssh/sshd_config' file. Soecifically, I changed the lines "PermitRootLogin without-password" to "PermitRootLoging no", and "PasswordAuthenticaion yes" to "PasswordAuthentication no". I then restarted my ssh service to activate the changes:

```
$ sudo nano /etc/ssh/sshd_config
$ sudo service ssh restart
```

## Prepare to Deploy your project

I reset my timezone to UTC with the command `sudo dpkg-reconfigure tzdata`. 

I then installed Apache, python setuptools, and libapache2-mod-wsgi for python3. I then restarted Apache:

```
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi-py3
$ sudo apt-get install python-setuptools
$ sudo service apache2 restart
```

I then installed PostgreSQL, and checked to make sure remote connections were disallowed by looking at my pg_hba.conf file:

```
$ sudo apt-get install postgresql
$ sudo nano /etc/postgresql/10/main/pg_hba.conf
```

I then switched to my postgres user, and used the 'psql' command to enter my PostgreSQL shell. While in the psql shell I created a new database called 'catalog', create a new user called 'catalog', set the password to 'catalog', and changed the pvivileges of the catalog database to allow all privileges for the catalog user:

```
$ sudo su - postgres
$ psql
postgres=# CREATE DATABASE catalog;
# CREATE USER catalog;
# ALTER ROLE catalog WITH PASSWORD 'catalog'
# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```

Finally, I switched back to my grader user by pressing 'ctrl+D' and installed git, then set the permisions to read, write and execute for the grader user only:

```
$ sudo apt-get install git
$ sudo chmod 700
```

## Deploy the item catalog project

I created a directory called 'catalog' in my '/var/www/' directory, then cloned my item catalog project into the 'catalog' directory:

```
$ cd mkdir /var/www/
$ mkdir catalog
$ cd catalog
$ git init
$ git clone https://github.com/matt-byers-redmarker/catalogue-app-submission.git
```

In order for my app to run correctly, I renamed my 'catalogue_application.py' file to '__init__.py'. I also renamed my 'database_setup.py' file to 'models.py' and moved it one directory up into my 'catalog' directory. I also moved my 'lots_of_categories.py' file up one directory. I then changed the 'create_enine' functionality inside my 'models.py', 'init.py', and 'lots_of_categories.py' file 

```
$ cd /catalogue-app-submission/
$ mv catalogue_application.py __init.py
$ mv database_setup.py models.py
$ mv /var/www/catalog/catalogue_app_submission/models.py ..
$ mv /var/www/catalog/catalogue_app_submission/lots_of_categories.py ..
```
Next , I created the file '/etc/apache2/sites-available/catalog.conf' with the command `$ sudo nano /etc/apache2/sites-available/catalog.conf` and inserted the following lines:

```
<VirtualHost *:80>
		ServerName 13.236.160.80
		ServerAdmin matthew.byers2@gmail.com
		WSGIScriptAlias / /var/www/catalog/catalog.wsgi
		<Directory /var/www/catalog/catalogue_app_submission/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/catalog/catalogue_app_submission/static
		<Directory /var/www/catalog/catalogue_app_submission/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel info
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

I then cd'ed to the '/var/www/catalog' directory, created the file 'catalog.wsgi' with `$ touch catalog.wsgi` and added the following lines:

```
#!/usr/bin/python3
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalogue_app_submission import app as application
application.secret_key = 'super_secret_key'
```

Then I cd'ed into my 'catalog' directory and ran my databse populating script with the command `python3 lots_of_categories.py'.

Finally, I restarted Apache with `$ sudo service apache2 restart`, and accessed my web app at http://13.236.160.80/

## Third party resources used

How To Deploy a Flask Application on an Ubuntu VPS - Kundan Singh, DigitalOcean
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

How To Secure PostgreSQL on an Ubuntu VPS
https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps#do-not-allow-remote-connections
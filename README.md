# Udacity-linux-server-config

This project aims to host a Flask application on a remote server through Amazon Lightsail. The application serves data using a PostgreSQL database, and has been configured to served a sports catalop application as a WSGI app.

## Server information

IP Address: 13.236.160.80
URL: 
SSH Port: 2200
SSH Key: 

# Summary of software installed



* [Vagrant](https://www.vagrantup.com/downloads.html)
* [Virtualbox](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)
* [Python 3](https://www.python.org/downloads/)

## Summary of configurations made

### Get your server

I created an account and setup an Ubuntu server through Amazon Lightsail. So I could SSH into my instance on a different port to port 22, I created a 'Custom TCP' rule in the 'Networking' tab of my Lightsail instance homepage and set the port to 2200.

### Secure your server

I updated all packages by running `sudo apt-get update` followed by `sudo apt-get upgrade`

I then changed the SSH port from 22 to 2200 by changing line 13 in /etc/ssh/sshd_config using the command `sudo nano  /etc/ssh/sshd_config`. I uncommented the line 'Port 22' and changed it to 'Port 2200'

I then configured by firewall by running the following commands:

`sudo ufw status` = shows status of ufw firewall, made sure it was disabled

`sudo ufw default allow outgoing` = set default to allow outgoing connections

`sudo ufw default deny incoming` = set default to deny incoming connections

`sudo ufw allow ssh` = allow ssh connections

`sudo ufw allow 2200/tcp` = allows access to the SSH port that we configured the Virtual Machine to run on in the previous step

`sudo ufw allow 80`= allows http connections

`sudo ufw allow 123`= allows NTP connections

`sudo ufw show added` = this allowed me to see all the rules I had set to ensure I had the correct setup before enabling the server

`sudo ufw enable` = this command starts the firewall

`sudo ufw status` = ran this command to check that the firewall was running. I saw that the status was 'active' and the rules I had configured were set up

### Give `grader` access

Next, I created a user called grader using the command `sudo adduser grader` to allow the Udacity grader to log into my project.

I then gave grader sudo access by adding the line `grader ALL=(ALL) NOPASSWD:ALL` to the file /etc/sudoers.d/grader using the command `sudo nano /etc/sudoers.d/grader`

The next step was to setup SSH keypair login for the grader user. First, I created a keypair in an .ssh directory in my Vagrant user's directory on my Vagrant VM. 






## Third party resources used

How To Deploy a Flask Application on an Ubuntu VPS - Kundan Singh, DigitalOcean
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

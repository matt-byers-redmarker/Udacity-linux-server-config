# Udacity-linux-server-config

This project aims to host a Flask application on a remote server through Amazon Lightsail. The application serves data using a PostgreSQL database, and has been configured to served a sports catalop application as a WSGI app.

## Server information

IP Address: 13.236.160.80

URL: 

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

I then copied the contents of 'grader.pub' by running `$ sudo cat grader.pub' and copying the contents of the file






## Third party resources used

How To Deploy a Flask Application on an Ubuntu VPS - Kundan Singh, DigitalOcean
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

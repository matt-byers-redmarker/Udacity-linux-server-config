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

I created an account and setup an Ubuntu server through Amazon Lightsail. So I could SSH into 

### Secure your server

I updated all packages by running `sudo apt-get update` followed by `sudo apt-get upgrade`

## Third party resources used

How To Deploy a Flask Application on an Ubuntu VPS - Kundan Singh, DigitalOcean
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

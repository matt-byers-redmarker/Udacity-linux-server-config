# Udacity-linux-server-config

The aim of this project is to produce a python script which queries a database of news data to answer three key reporting questions: 
* What are the most popular three articles of all time?
* Who are the most popular article authors of all time?
* On which day/s did more than 1% of requests lead to errors?

## Requirements for setup

This script is run through a Linux-based virtual machine. For this project, you must have Vagrant and Virtualbox set up on your machine. This script also uses python 3.7.4, so python 3 must be installed. To install all of these, follow the instructions in the links below:

* [Vagrant](https://www.vagrantup.com/downloads.html)
* [Virtualbox](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)
* [Python 3](https://www.python.org/downloads/)

## Summary of configurations made

1. Download and install Vagrant, Virtualbox and Python 3.
2. Download this repository and ensure it has each file mentioned above.
3. Open a terminal, git bash, or other command line tool.
4. cd into your directory containing the 'vagrant' folder installed after downloading vagrant.
5. Launch the virtual machine with `vagrant up`
6. Finalise launch of the virtual machine with `vagrant ssh`
7. This will start a new command line instance in your virtual machine. While in here cd into your /vagrant folder.
8. Move the contents of this repository into your vagrant folder.
9. Load the database with `psql -d news -f newsdata.sql`
10. Connect to the database with `\c news`
11. Create the 'daily_error_rate' VIEW with the create view command in the Views section below.
11. Run the script with `python news_report.py` to query the database. You should see the output of the script in your terminal.

## Third party resources used

How To Deploy a Flask Application on an Ubuntu VPS - Kundan Singh, DigitalOcean
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

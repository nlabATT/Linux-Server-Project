# Udacity Project - Linux Server Configuration

### Project Overview
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

!!!

## Step by Step Walkthrough in completing this project:

1. To start you'll need a Linux server instance. Udacity recommend using Amazon Lightsail. If you don't already have an Amazon Web Services account, you'll need to set one up. [https://aws.amazon.com]
	Start a new Ubuntu Linux server instance on Amazon Lightsail

1. Log in!
First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.

Amazon Web Services login page

2. Create an instance.
Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

Lightsail screenshot: No instances
When you have no instances, Lightsail gives you a picture of an orange robot and suggests that you create an instance.

3. Choose an instance image: Ubuntu
Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications.

For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.

Lightsail setup: Pick your instance image (Ubuntu)
When you create an instance, Lightsail asks what kind you want.
For this project, choose an "OS Only" instance with Ubuntu.

4. Choose your instance plan.
The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine. And as long as you complete the project within a month and shut your instance down, the price will be zero.

Lightsail setup: Pricing options. (Choose $5/month with first month free.)
Lightsail's options for instance pricing.
For this project, pick the lowest one to get free-tier access.

Be aware: If you enable additional features in Lightsail, you may be charged extra for them.

5. Give your instance a hostname.
Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. Your instance's name will be visible to you and to the project reviewer.

Lightsail setup: Setting a name for your instance.
I've named my instance silly-name-here.

6. Wait for it to start up.
It may take a few minutes for your instance to start up.

Lightsail: Instance starting.
While your instance is starting up, Lightsail shows you a grayed-out display.

Lightsail: Instance running.
Once your instance is running, the display gets brighter.

7. It's running; let's use it!
Once your instance has started up, you can log into it with SSH from your browser.



2. Setting up ssh and terminal emulator putty.

	a. link to putty installer
	b. helpful resources https://www.youtube.com/watch?v=HcwK8IWc-a8
	c. https://classroom.udacity.com/courses/ud595-nd  Linux Command Line Basics
	d. https://classroom.udacity.com/courses/ud299-nd Linux Web Server Basics

3. Once familiar with the ssh basics.
	
	a. Update all currently installed packages.
	b. Change the SSH port from 22 to 2200. Not to forget to configure the Lightsail firewall to allow it.
	c. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).


4. Setting up new User 

	a. Create new user account named grader and setup permission
	
	sudo adduser grader --> creates user grader, enter your chosen password and filled misc info when desired.
	other commands:
	sudo ls /etc/sudoers
	sudo ls /etc/sudoers.d
	sudo adduser grader
	sudo cp /etc/sudoers.d/vagrant /etc/sudoers.d/grader
	sudo nano /etc/sudoers.d/grader
	sudo passwd -e student

	b. Create an SSH key pair for grader using the ssh-keygen tool.

	ssh-keygen

	other useful commands:
	ssh grader@18.223.228.41 -p 2200
	mkdir .ssh
	touch .ssh/authorized_keys
	nano .ssh/authorized_keys
	chmod 700 .ssh
	chmod 644 .ssh/authorized_keys
	ssh grader@34.235.148.10 -p 2200 -i ~/.ssh/mygrader
	ssh -i ~/.ssh/mygrader * ubuntu@34.235.148.10:/home/ubuntu/catalog  -p 2200 
	
	vi /etc/ssh/sshd_config
	sudo nano /etc/ssh/sshd_config
	PermitRootLogin from without-password to no
	sudo chgrp root .bashrc
	service sshd restart

	sudo ufw status
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow ssh
	sudo ufw allow 2200/tcp
	sudo ufw allow www
	sudo ufw enable

	sudo apt-get update
	sudo apt-get dist-upgrade

	$ sudo apt-get install unattended-upgrades
	$ sudo dpkg-reconfigure -plow unattended-upgrades

	PermitRootLogin from without-password to no

5. Configure the local timezone to UTC.
Open the timezone selection dialog:
$ sudo dpkg-reconfigure tzdata
Then chose 'None of the above', then UTC.
*Setup the ntp daemon ntpd for regular and improving time sync:
$ sudo apt-get install ntp
*Chose closer NTP time servers:
Open the NTP configuration file:
$ sudo vim /etc/ntp.conf
Open http://www.pool.ntp.org/en/ and choose the pool zone closest to you and replace the given servers with the new server list.
9 - Install and configure Apache to serve a Python mod_wsgi application

sudo reboot


Prepare Web Application Server: Thru prepared SSH connection.

6. Install and configure Apache to serve a Python mod_wsgi application.

sudo apt-get install apache2

Verify Apache is working by visiting 

http://localhost:8080 or alternately
http://your_public_address_from_Linux server instance

you should see the Apache Ubuntu Default page

Once Apache is installed you can now configure to perform more.

If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: 

sudo apt-get install libapache2-mod-wsgi

Or if you are working with Python 3

sudo apt-get install libapache2-mod-wsgi-py3

by default you can edit 

sudo nano /etc/apache2/sites-enabled/000-default.conf 

or alternately make your own configuration

sudo nano /etc/apache2/sites-available/catalog.conf
/var/www/html/myapp.wsgi

Enable mod_wsgi: $ sudo a2enmod wsgi.
$ sudo service apache2 start.
sudo apache2ctl restart

7. Copy you own Application created from Lesson 3 "Catalog Project" or use Github 
and clone to desired directory


to   Install and configure PostgreSQL:

Do not allow remote connections
Create a new database user named catalog that has limited permissions to your catalog application database.
12. Install git.

Deploy the Item Catalog project.
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!

!!!!!!


Submission
Please follow these steps to properly submit this project:

Create a new GitHub repository and add a file named README.md.

Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.

When you're ready to submit your project, click here and follow the instructions. Due to the high volume of submissions we receive, please allow up up to 7 business days for your evaluation to be returned.

If you are having any problems submitting your project or wish to check up on the status of your evaluation, please email us at **fullstack-project@udacity.com**.

Next Steps
You will get an email as soon as your reviewer has feedback for you. Congratulations on making it this far in the Nanodegree! You're almost finished!

You have not submitted the project yet



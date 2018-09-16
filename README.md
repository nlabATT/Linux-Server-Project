# Udacity Project - Linux Server Configuration

### Project Overview
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

!!!
__IP Address__ : 34.235.148.10
__ssh Port__ : 2200
__URL__: 34.235.148.10.xip.io

## Step by Step Walkthrough in completing this project:
Source and References:
[Amazon Web Services(https://aws.amazon.com/) 


1. To start you'll need a Linux server instance. __Udacity__ recommend using ___AWS Lightsail___. If you don't already have an Amazon Web Services account, you'll need to set one up.
	
  Start a new Ubuntu Linux server instance on Amazon Lightsail

  a. Log in!
     First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.

  b. Create an instance.
     A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

  c. Choose an instance image: Ubuntu
     For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. First, choose "OS Only". Second, choose Ubuntu as the operating system.

  d. Choose your instance plan.
     For this project, I pick the lowest one to get free-tier access.

  e. Give your instance a name.
     Every instance needs a unique hostname.

  f. Wait for it to start up.
     While your instance is starting up, Lightsail shows you a grayed-out display.
     Once your instance is running, the display gets brighter.

2. Secure and Set-up server.
__Source__:[Link to Putty Installer][https://www.putty.org/]
__Helpful Resources__:
[Udacity Configuring Linux Server][https://classroom.udacity.com/courses/ud299]
[How to Access a Remote Server and Edit Files][https://www.youtube.com/watch?v=HcwK8IWc-a8]
[Linux Command Line Basics][https://classroom.udacity.com/courses/ud595-nd]  
[Linux Web Server Basics][https://classroom.udacity.com/courses/ud299-nd]

  a. Once familiar with the ssh basics.
		a.1. Update all currently installed packages.
        $ sudo apt-get update
        $ sudo apt-get dist-upgrade
        $ sudo apt-get install unattended-upgrades
        $ sudo dpkg-reconfigure -plow unattended-upgrades
        $ sudo reboot --> run when needed or you see a message needed to 
          restart after logging in to ubuntu thru ssh.

	  a.2. Create custom SSH port 2200 in AWS lightsail
    a.3. Create custom NTP port 123/UDP in AWS lightsail

  b. Setting up new User 
  	b.1. Create new user account named grader
	      $ sudo adduser grader 
	
        other helpful commands:
	      $ sudo ls /etc/sudoers
	      $ sudo ls /etc/sudoers.d
        $ sudo cp /etc/sudoers.d/ubuntu /etc/sudoers.d/grader
        $ sudo nano /etc/sudoers.d/grader
        $ sudo passwd -e grader

    _Note_: read __b.2__ to support some commands above.
          : link above ___Configuring Linux Server___ is very helpful     

	  b.2. Create an SSH key pair for grader using the ssh-keygen tool.
        
         __Useful commands:__
        $ ssh grader@your-public-IP-address -p 2200
        _Note:_ before using ___ssh___ to user grader temporarily activate Password Authentication and set it to __yes__
        $ sudo service sshd restart --> command to activate changes done to 
          file __sshd_config__ 
        $ sudo nano /etc/ssh/sshd_config
        $ mkdir .ssh
        $ touch .ssh/authorized_keys
        $ nano .ssh/authorized_keys
        $ chmod 700 .ssh
        $ chmod 644 .ssh/authorized_keys
        $ ssh -i ~/.ssh/private-filename-created-from-ssh-keygen
          grader@your public-IP-address -p 2200 
        $ chgrp root .bashrc

  c. Configure the Uncomplicated Firewall (UFW)    
        $ sudo ufw status
        $ sudo ufw default deny incoming
        $ sudo ufw default allow outgoing
        $ sudo ufw allow 2200/tcp
        $ sudo ufw allow www
        $ sudo ufw allow ntp/udp
        $ sudo ufw enable

  d. Configure the local timezone to UTC.
  __Resource:__ [github:Stueken Linux-Server-Configuration-Project][https://github.com/stueken/FSND-P5_Linux-Server-Configuration]
        Open the timezone selection dialog:
        $ sudo dpkg-reconfigure tzdata
        Then chose 'None of the above', then UTC.
        *Setup the ntp daemon ntpd for regular and improving time sync:
        $ sudo apt-get install ntp
        *Chose closer NTP time servers:
        Open the NTP configuration file:
        $ sudo nano /etc/ntp.conf
        Open [http://www.pool.ntp.org/en/] and choose the pool zone closest to you and replace the given servers with the new server list.
        9 - Install and configure Apache to serve a Python mod_wsgi application

  e. Set Server to not accept root login.

        $ sudo nano /etc/ssh/sshd_config

        PermitRootLogin from ___old_value___ to __no__
        $ sudo service sshd restart


3. Prepare Web Application Server:
__Source:__ [DigitalOcean][https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps] 

  a. Install and configure Apache to serve a Python mod_wsgi application.

    $ sudo apt-get install apache2

    Verify Apache is working by visiting below in your browser 

    [http://localhost] or alternately
    [http://your_public_address_from_Linux_server_instance]

    you should see the Apache Ubuntu Default page

    $ sudo apt-get install libapache2-mod-wsgi-py3  


    Once Apache is installed you can now configure to perform more.

  by Default:
            /var/www/html   --> html page and wsgi files 
            /etc/apache2/sites-enabled/000-default.conf
    
      $ sudo nano /etc/apache2/sites-enabled/000-default.conf 
      when performing test changes from default
      $ sudo apache2ctl restart --> to restart default configuration

      ### or alternately make your own configuration

      $ sudo nano /etc/apache2/sites-available/catalog.conf
```sample catalog.conf
          <VirtualHost *:80>
                ServerName 34-235-148-10
                ServerAdmin admin@34.235.148.10
                WSGIDaemonProcess movieproject user=ubuntu group=ubuntu threads=5 home=/var/www/catalog/
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog>
                    WSGIProcessGroup movieproject
                    WSGIApplicationGroup %{GLOBAL}
                    WSGIScriptReloading On
                    Require all granted
           </VirtualHost>
```

      $ sudo nano /var/www/catalog/catalog.wsgi      
```sample wsgi file
            #!/usr/bin/python
            
            import sys
            sys.path.insert(0,"/var/www/catalog/")
            
            from movieproject import app as application
```
__Note:__ very important to match the value of movieproject to your python application filename and point to the correct directory where the application is located.

      $ sudo a2enmod wsgi --> to enable wsgi
      $ sudo a2enmod catalog --> to enable catalog
      $ sudo service apache2 restart --> to restart catalog configuration
      $ sudo apache2ctl restart --> to restart configuration

  b. Install and configure PostgreSQL
  __Source:__ [Stueken Github Project][https://github.com/stueken/FSND-P5_Linux-Server-Configuration]   
              [Stackoverflow Sqlalchemy][https://stackoverflow.com/questions/22353512/how-to-install-sqlalchemy-on-ubuntu] 
      Install PostgreSQL:
      $ sudo apt-get install postgresql postgresql-contrib
      Check that no remote connections are allowed (default):
      $ sudo nano /etc/postgresql/9.5/main/pg_hba.conf  --> verify folder what 
                                                            version
      Open the database setup file:
      $ sudo nano movie_database_setup.py
      Change the line starting with "engine": 
        format : engine = create_engine('postgresql://_user:password@localhost/dbname_)
        ex. engine = create_engine('postgresql://catalog:PW-FOR-DB@localhost/catalog')
      Create needed linux user for psql:
      $ sudo adduser catalog (choose a password)
      Login to database with superuser postgres:
      $ sudo su - postgres
      Connect to the system:
      $ psql
      Add postgres user with password:
      Create user with LOGIN role and set a password:
      # CREATE USER catalog WITH PASSWORD 'PW-FOR-DB'; (# stands for the command prompt in psql)
      Allow the user to create database tables:
      # ALTER USER catalog CREATEDB;
      *List current roles and their attributes: # \du
      Create database:
      # CREATE DATABASE catalog WITH OWNER catalog;
      Connect to the database catalog # \c catalog
      Revoke all rights:
      # REVOKE ALL ON SCHEMA public FROM public;
      Grant only access to the catalog role:
      # GRANT ALL ON SCHEMA public TO catalog;
      Exit out of PostgreSQl and the postgres user:
      # \q, then $ exit
      Create postgreSQL database schema:
      $ python movie_database_setup.py

  c. Copy you own Application created from Lesson 3 "Catalog Project" or use Github and clone to desired directory
    _format_: $ scp -i ~/.ssh/privatekey -P 2200 files-to-copy ubuntu@your-IP-adress:/var/www/catalog
      $ scp -i ~/.ssh/privatekey -P 2200 * ubuntu@34.235.148.10:/var/www/catalog
      $ cd catalog 
      $ sudo mkdir static templates

  d. Install virtual environment, Flask and the project's dependencies
  _Note:_ Change directory to Application folder ex. /var/www/catalog
      Install pip, the tool for installing Python packages: 
      $ sudo apt-get install python-pip.
      If virtualenv is not installed, use pip to install it using the following command: 
      $ sudo pip install virtualenv.
      Create new virtual environment with the following command: 
      $ sudo virtualenv venv.
      Activate the virtual environment: 
      $ source venv/bin/activate.
      Change permissions to the virtual environment folder: 
      $ sudo chmod -R 777 venv.
      Install Flask: 
      $ pip install Flask.
      $ pip install bleach
      $ pip install httplib2
      $ pip install requests
      $ pip install oauth2client
      $ pip install sqlalchemy or 
      $ sudo easy_install sqlalchemy
      $ pip install python-psycopg2

4. Be ready to Start Application

  a. Restart configuration
     $ sudo apache2ctl restart restart configuration
  b. Verify ErrorLog for troubleshooting when App do not work
     sudo cat /var/log/apache2/error.log

5. Do not be discourage when get stuck it is an oppurtunity to learn.

# Have fun fun fun !!!!!

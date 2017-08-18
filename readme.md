Linux Server Configuration project

We are deploying the flask application (Flask Catalog as part of the Udacity Full-Stack Nanodegree) in the following to Digital Ocean a Linux Ubuntu Instance. 

https://github.com/bwong199/flask_catalog

This is the IP address: 107.170.226.193 and the url to the project: http://107.170.226.193/

The following steps in the article are the steps I took to set up an instance: 

https://www.digitalocean.com/community/tutorials/how-to-create-your-first-digitalocean-droplet-virtual-server

Server Update with the following: 

sudo apt-get update
sudo apt-get upgrade

User Management Configuration:

1) Add a new user called grader with the following and gave the user a password:

sudo adduser grader

2) Give Grader sudo priviledge with the following:

- sudo visudo
- Add grader  ALL=(ALL:ALL) ALL below root    ALL=(ALL:ALL) ALL


Security and Firewall:

1) Remove port 22 from the Digital Ocean console and add port 2200
2) Run sudo nano /etc/ssh/sshd_config:

# What ports, IPs and protocols we listen for
# Port 22
Port 2200 ### Change to port 2200

# Authentication:
LoginGraceTime 120
#PermitRootLogin prohibit-password
PermitRootLogin no ### Disable root remote login
StrictModes yes

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication No



3) Run the following to run SSH: 
	sudo service restart ssh 
4) Run the following to config SSH to run on 2200, HTTP to run on 80 and NTP to run on 123

sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp or sudo ufw allow www (either one of these commands will work)
sudo ufw allow 123/udp

The changes should be reflected when you run sudo ufw enable. Run sudo ufw status to see the changes. 

5) Run the following to configure time zone: 

sudo dpkg-reconfigure tzdata

6) To create SSH keys to ssh to the instance:
	- Run ssh-keygen in the local machine and go through the steps 
	- In the Digital Ocean instance, run 1) mkdir .ssh and 2) cd .ssh to open the .ssh folder
	- Run "sudo nano authorized_keys" and run pbcopy < ~/.ssh/id_rsa.pub in your LOCAL machine to copy the generated key
	- Ctrl-V to paste the key to the VI editor, save and close. 

7) Now in your LOCAL machine directory if you run ssh user@PublicIP -p 2200 -i ~/.ssh/whateverfile_id_rsa, you will log into your Digital Ocean Ubuntu instance from your LOCAL machine


Instance Server Configuration and App Configuration:

1) Install nginx (Apache alternative) with "sudo apt-get install nginx"
2) Install MariaDB and other dependencies with "sudo apt-get install mariadb-server libmariadbclient-dev libssl-dev"
3) To verify database was installed, run "mysql -uroot -p" and you should log into the DB CLI. 
4) Create the Database with "CREATE DATABASE CATALOG"
5) Create a folder for the files to be cloned from GIT with "MKDIR app"
6) Clone the files from previously committed Item Catalog project to the app directory
7) Run "PIP install -r requirements.txt" to install all the app's dependencies
8) Run "pip install gunicorn" to install python WSGI HTTP server
9) In the app directory, run "vi wsgi.py" and paste the following to configure the WSGI file:

import os, sys
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))

from flask_catalog import app

if __name__ == "__main__":
    app.run()


10) Run genicorn --bind 0.0.0.0:80 wsgi:app to bind port 80 to gunicorn
11) Run "python manage.py db init", then "python manage.py db migrate" to setup all application's database models
12) Go to "107.170.226.193" in your browser to see the application







# Linux Server Configuration Project
![Part of the Udacity Full Stack Web Development Nanodegree](https://img.shields.io/badge/Udacity-Full%20Stack%20Web%20Developer%20Nanodegree-lightgrey.svg)

In this project, we will take a baseline installation of a Linux server and prepare it to host web applications. We will secure the server from a number of attack vectors, install and configure a database server (Postgres), and configured it to serve the [Catalog] application as a WSGI app.

##### IP address and SSH port:
- ip: 18.221.245.209
- port: 2200

##### Complete URL to [Catalog] application
- url: http://18.221.245.209

### Softwares installed and configuration changes made

##### Update all currently installed packages
___
Run the following commands to update currently installed packages, and removes packages no longer needed:
```
# sudo apt-get update
# sudo apt-get upgrade
# sudo apt-get autoremove
```
#####  Change the SSH configuration
___
To change the SSH  port from 22 to 2200, disable root login, and password-based login through ssh:
Open sshd_config and change # Port 22 (to Port 2200), *PermitRootLogin no*, and *PasswordAuthentication no*.
```
$ sudo nano /etc/ssh/sshd_config
# Changes...
$ sudo service ssh restart
```
##### Configure the Uncomplicated Firewall (UFW)
___
Only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
First check the ufw status. Then, allow and deny according to the case( allow SSH (port 2200), HTTP (port 80), and NTP (port 123)), and finally enable the firewall:
```
$ sudo ufw status
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
$ sudo ufw allow ssh
$ sudo ufw allow 2200
$ sudo ufw allow www
$ sudo ufw allow 123
$ sudo ufw enable)
```
Since firewall is on, and SSH had beeen change to 2200, check if we still can connect via SSH:
```
 $ ssh ubuntu@18.221.245.209 -p 2200 -i ~/.ssh/ubuntu-key.pem
 # Note: In the Lightsail instance we have to add the TCP 2200 port
```
##### Create a new user account named grader
___
Set the password to 'password', and use the command **finger** to check if the new user has been created successfully and see the user's details.
```
$ sudo adduser grader
$ finger grader
```
##### Give grader the permission to sudo
___
Create *grader* file inside *sudoers.d* directory, and add this text: **grader ALL=(ALL) NOPASSWD:ALL**
```
$ touch /etc/sudoers.d/grader
```
##### Create an SSH key pair for grader
___
Generate the keys using the ssh-keygen tool:
```
$ ssh-keygen
```
Log in with the user *grader* using the password. For that we have to set **PasswordAuthentication yes** in the configuration file: */etc/ssh/sshd_config*, later set it back to **no**, so Key-based SSH authentication is enforced.
Then, create *.ssh/authorized_keys* and copy the content of the public key generated before (*grader_key.pub*). Finally apply permissions:

```
$ mkdir .ssh
$ touch .ssh/authorized_keys
$ nano .ssh/authorized_keys
# paste the contents and save the file
$ chmod 700 .ssh
$ chmod 644 .ssh/authorized_keys
```
##### Configure the local timezone to UTC
To configure timezone:
```
$ sudo dpkg-reconfigure tzdata
# None of the above and then UTC
```
##### Install and configure Apache to serve a Python mod_wsgi application
___
To serve Python using Apache and mod_wsgi, install:
```
$ sudo apt-get install python3
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi
```
To configure Apache to handle requests using the WSGI module. You’ll do this by editing the */etc/apache2/sites-enabled/000-default.conf* file and  add the following line at the end of the *<VirtualHost>* section: **WSGIScriptAlias / /var/www/html/catalog.wsgi**. Now, *catalog.wsgi* will be the one the server execute instead of *index.html*.
Finally, restart Apache:
```
$ sudo apache2ctl restart
```
##### Install and configure PostgreSQL
___
Install postgresql:
```
$ sudo apt-get install postgresql
```
Double check that no remote connections are allowed by looking in the host based authentication file:
```
$ sudo nano /etc/postgresql/9.5/main/pg_hba.conf
```
Now, create a new database user named *catalog* that has limited permissions to the catalog application database.
```
# To get into psql shell
$ sudo -u postgres psql
postgres=# create user catalog with password 'password';
postgres=# create database catalog with owner catalog;
postgres=# grant all privileges on database catalog to catalog;
```
##### Install git and clone the catalog project
___
Like before, use *apt-get* command to install git, then clone *catalog* inside *www* directory:
```
$ sudo apt-get install git
$ cd /var/www/html
$ sudo git clone https://github.com/oisbel/catalog-project.git
# Use mv  -v to make sure all the files are at /var/www/html
```
##### Set up catalog project
___
To run the application you need a .wsgi file with the code mod_wsgi executing on startup to get the application object.So, inside */var/www/html* create catalog.wsgi and add the following into the file:
> import sys
> sys.path.insert(0, '/var/www/html/')
> from application import app as application
> application.secret_key = '88040422507vryyo'
> #application is the name of the .py program

Since catalog project is using **SQLite**, change the setup in each file come with the database connection( to **PostgreSQL**):
> engine = create_engine('postgresql://catalog:password@localhost/catalog')

Install python packages:
```
$ apt-get install python-pip
$ pip install Flask
$ sudo pip install requests
$ sudo pip install sqlalchemy
$ sudo pip install psycopg2
$ sudo pip install oauth2client
$ sudo pip install httplib2
```
##### Set it up in your server
___
To create tables and populate the database execute:
```
$ cd /var/www/html/
$ python database.py
$ python additems.py
$ sudo apache2ctl restart
```
Finally, when open http://18.221.245.209 we should see the Catalog-Music website(if not, check error: *sudo cat /var/log/apache2/error.log*)

##### Google OAuth Problem

Since Google OAuth doesn't work for bare IPs, to get log into the website we have to create a domain name for the server.
Then, in the OAuth credential we have to add this domain to *Authorized JavaScript origins*, that way we don't get *Error: origin_mismatch* from Google.

### Resources Used
- Udacity Course: Deploying to Linux Servers
- [Deploying-mod_wsgi (Apache)]

   [Catalog]: <https://github.com/oisbel/catalog-project.git>
   [Deploying-mod_wsgi (Apache)]:<http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/>
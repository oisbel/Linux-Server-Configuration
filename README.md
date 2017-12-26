# Linux Server Configuration Project
![Part of the Udacity Full Stack Web Development Nanodegree](https://img.shields.io/badge/Udacity-Full%20Stack%20Web%20Developer%20Nanodegree-lightgrey.svg)

In this project, we will take a baseline installation of a Linux server and prepare it to host web applications. We will secure the server from a number of attack vectors, install and configure a database server (Postgres), and configured it to serve the [Catalog] application as a WSGI app.

### i. IP address and SSH port

### ii. Complete URL to [Catalog] application

### iii. Softwares installed and configuration changes made

##### Update all currently installed packages( step 3)
___
Run the following commands to update currently installed packages, and removes packages no longer needed:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get autoremove
```
#####  Change the SSH port from 22 to 2200
___
Open sshd_config and change # Port 22 (to Port 2200):
```
sudo nano /etc/ssh/sshd_config
sudo service ssh restart
```
##### Configure the Uncomplicated Firewall (UFW)
___
Only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
First check the ufw status. Then allow and deny according to the case( allow SSH (port 2200), HTTP (port 80), and NTP (port 123)), and finally enable the firewall:
```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 2200
sudo ufw allow www
sudo ufw allow 123
sudo ufw enable)
```
Since firewall is on, and SSH had beeen change to 2200, we check if we still can connect via SSH:
```
 ssh -p 2200 -i ~/.ssh/ubuntu-key.pem ubuntu@13.58.126.0
 # Note: In the Lightsail instance we have to add the TCP 2200 port
```
##### Create a new user account named grader
___
We set the password to 'password', and use the command **finger** to check if the new user has been created successfully and see the user's details.
```
sudo adduser grader
finger grader
```

### Resources Used
- StackOverflow
- Knockout JS Documentation
- Google Maps API Documentation
- Udacity The Frontend: JavaScript & AJAX Course

   [Catalog]: <https://github.com/oisbel/catalog-project.git>
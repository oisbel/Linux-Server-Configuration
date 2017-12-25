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
Open sshd_config and change # Port 22 (to Port 2200)
```
sudo nano /etc/ssh/sshd_config
sudo service ssh restart
```

### Resources Used
- StackOverflow
- Knockout JS Documentation
- Google Maps API Documentation
- Udacity The Frontend: JavaScript & AJAX Course

   [Catalog]: <https://github.com/oisbel/catalog-project.git>
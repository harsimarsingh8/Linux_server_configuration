# Linux Server Configuration

> Harsimar Singh

-----------------------------------------------------------

* The live streaming of [project](13.232.77.118).
* Project can be viewed at my [Github Repository](https://github.com/harsimarsingh8/Linux_server_configuration).

------------------------------------------------------------
# About

This is the sixth project for the Udacity Full Stack Nanodegree and involves how to access, secure, and perform the initial configuration of a bare-bones Linux server by a  baseline installation of a Linux distribution on a virtual machine and prepare it to host web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.


# Why this Project?

>A deep understanding of exactly what web applications are doing, how they are hosted, and the interactions between multiple systems are. This project, turns a brand-new, bare bones, Linux server into the secure and efficient web application host that a Data Driven Web Applications Needs. In short it includes all the steps that are required to be a full stack developer.

# Server details for hosting applications

- **Public IP:** 13.232.77.118
- **Port:** 2200
- Live streaming of project: [Restaurant_Item_App](http://13.232.77.118/)

# In this we will be configuring a linux server

# Setting up

This project uses [Amazon Lightsail](https://amazonlightsail.com/) to create a Linux server instance.

### You need to have a amazon aws account if not then create a one and we have 12 months free access to aws services.

1. Get your server.

    - Start a new Ubuntu Linux server instance on Amazon Lightsail. 
        
        * Log in!
        * Create an instance
        * Choose an instance image: Ubuntu (OS only)
        * Choose your instance plan (lowest tier is fine)
        * Give your instance a hostname
        * Wait for startup
        * Once the instance has started up, follow the instructions provided to SSH into your server.

#  Following are the Steps to Configure Linux server

### 1. Create (IDLE)Development Environment Instance.

  * [Create new development environment.](https://www.udacity.com/account#!/development_environmet)

  * Download private key and write down your public IP address(can be found on created instance dashboard)

### 2. Virtual Machine is launched and for login we use SSH into the server.

  * Move private key file into the desired folder.
  * we have our key in aws.pem file (the following file is in the given folder) 
  * we store it in aws.ppk which we get while installing our instance
  * and instead of ssh agin and again we had added the aws.ppk file in ssh/auth folder
  * then save it on required port and save it and load it .
  * to login into ubuntu we just double click the name we added while loading the hostname(public IP) we got while    
    making an instance on required port.


# Prerequisite
- for linux users:

they can go with their linux CLI

- for windows users:

they need to install putty for running below commands

# Putty

PuTTY is a free and open-source terminal emulator, serial console and network file transfer application. It supports several network protocols, including SCP, SSH, Telnet, rlogin, and raw socket connection. It can also connect to a serial port.

# How To Run

I decided to start the project by the ufw configuration. Hence, if I got blocked out from the server, it would happen in the very beginning.

Configure the Uncomplicated Firewall (UFW)

  ```
    $ sudo ufw default deny incoming
    $ sudo ufw default allow outgoing
    $ sudo ufw allow 2200/tcp
    $ sudo ufw allow www
    $ sudo ufw allow ntp
    $ sudo ufw enable
  ```

#  we need to take care of server by securing it.

- Update all currently installed packages.
    
            sudo apt-get update
            sudo apt-get upgrade
            
        auto upgrades run
             sudo apt-get install unattended-upgrades


### we now need to have all the dependencies and modules installed to run our project that we used in our project


- following are the two methods to install dependencies
1. Write all the following commands:

* `sudo apt-get install apache2 libapache2-mod-wsgi git`
* Enable mod_wsgi: `sudo a2enmod wsgi`
* `sudo apt-get install libpq-dev python-dev`
* `sudo apt-get install postgresql postgresql-contrib`
* `sudo apt-get install python-pip`
* `sudo pip install Flask`
* `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`
* `sudo pip install requests`

2. We can also write all the dependencies in .txt file with names and version
   and then use the following command to install them at once instead of typing manually

- pip install -r requirements.txt


# Change SSH port from 22 to 2200
Edit the file `/etc/ssh/sshd_config` by(`sudo nano /etc/ssh/sshd_config`) and change the line `Port 22` to:

`Port 2200`

# Blocking the connection to port 22
[Getting Started with UFW](https://www.howtoforge.com/tutorial/ufw-uncomplicated-firewall-on-ubuntu-15-04/)

`ufw deny 22`

To check that the rules have changed:

`sudo ufw show added`

Then restart the SSH service:

`sudo service ssh restart`
Now when the port 22 is closed I always need to SSH to my instance as a remote user.

# Change timezone to UTC.

  * Check the timezone with the date command. This will display the current timezone after the time. If it's not UTC change it like this:

  ```
    $ sudo timedatectl set-timezone UTC
  ```
# Grader access.

   we have grader which need to be logged in to server by a user for project to be reviewed.

- Create a new user account named grader.
    
            sudo adduser grader
- Give grader the permission to sudo.
    
            sudo nano /etc/sudoers.d/grader
        add text `grader ALL=(ALL) NOPASSWD:ALL`
        do ctrl+x to save it then type y
- Create an SSH key pair for grader using the ssh-keygen tool.
            
                ssh-keygen -t rsa
                
            To login
                ssh -i .ssh/grader_udacity grader@34.210.194.203 -p 220

                     or 

             simply just double click the session created on required port
             and unique ip addres and you get prompt with login as write
             (grader)        

# Generating encryption key pair
 * We did this by just adding aws.ppk file as mentioned above

# Set-up SSH keys for user grader and installing public keys
As root user do:
```
mkdir /home/grader/.ssh
chown grader:grader /home/grader/.ssh
chmod 700 /home/grader/.ssh
cp /root/.ssh/authorized_keys /home/grader/.ssh/
chown grader:grader /home/grader/.ssh/authorized_keys
chmod 644 /home/grader/.ssh/authorized_keys
```

* Restart the service :`sudo service ssh restart`

# Log in as a grader user
* `ssh -i ~/.ssh/fileName grader@52.15.235.1 -p 2200`
          or
  just write grader in login as when you enter the putty


# Disabling root login and Forcing key based authentication
* `sudo nano /etc/ssh/sshd_config`
* Make changes inside the file by making PasswordaAthentication no 
  from yes and save the file
* Restart the service `sudo service ssh restart`

Change the following line in the file `/etc/ssh/sshd_config`:

From `PermitRootLogin without-password` to `PermitRootLogin no`.

Also, uncomment the following line so it reads:
```
PasswordAuthentication no
```

  * Login as *postgres* User (Default User), and get into PostgreSQL shell:

  ```
    $ sudo su - postgres
    $ psql
  ```

   * Create a new User named *catalog*:  `# CREATE USER catalog WITH PASSWORD 'password';`

   * Create a new DB named *catalog*: `# CREATE DATABASE catalog WITH OWNER catalog;`

   * Connect to the database *catalog* : `# \c catalog`

   * Revoke all rights: `# REVOKE ALL ON SCHEMA public FROM public;`

   * Lock down the permissions only to user *catalog*: `# GRANT ALL ON SCHEMA public TO catalog;`

   * Log out from PostgreSQL: `# \q`. Then return to the *grader* user: `$ exit`

  * Inside the Flask application, the database connection is now performed with:


# Clone the Catalog app from your Github where you made the item_catalog project

  * Make a *catalog* named directory in */var/www*

    ```
      $ sudo mkdir /var/www/catalog
    ```

  * Change the owner of the directory *catalog*

    ```
     $ sudo chown -R grader:grader /var/www/catalog
    ```

  * Clone the *Item_Catalog* to the catalog directory:

    ```
     $ git clone https://github.com/harsimarsingh8/item-catalog.git catalog
    ```

  * Change the branch of repo *Item_catalog*  to *production*:

    ```
     $ cd catalog && git checkout production
    ```

  * Make a catalog.wsgi file to serve the application over the mod_wsgi. with content:

    ```
     $ touch catalog.wsgi && nano catalog.wsgi
    ```

    ```
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from item_catalog import app as application
    ```

# This step is very important 
  - As had changed the branch on github also to production and made a new file catalog.wsgi which is doing all the   work while hosting from behind and we need to write following line wherever we created engine as we had changed the database.
  ```
  engine = create_engine('postgresql://catalog:password@localhost/catalog')
  ```
  - then push all the changes in the repository by writing above line wherever engine were created
  - then git pull the changes in the putty 
  - Now Inside *item_catalog.py*  database connection is now performed with:

    ```
     engine = create_engine('postgresql://catalog:password@localhost/catalog')
    ```
 
# Editing the virtual file
* `sudo nano /etc/apache2/sites-available/000-default.conf`
  and add the following content:
  
  ```
  <VirtualHost *:80>
    ServerName XX.XX.XX.XX
    ServerAdmin harsimar.singh08@gmail.com
    WSGIScriptAlias / /var/www/catalog/<filename.wsgi>
    <Directory /var/www/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/static
    <Directory /var/www/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
  </VirtualHost>
 
  ```
# Relaunch app
  
* `sudo service apache2 restart`

# Creating database and adding dumps to it
* `cd /var/www/catalog`
* `python database_setup.py`
* `python DisplayMenu_fake.py`     

# Again start the apache server after making changes
  
* `sudo service apache2 restart`


# external guidance and motivation
- Udacity FSND Forum discussions.
- Udacity mentor.
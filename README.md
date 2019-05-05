# Deploying Item-App on the website using AWS
This project is a practise on how to deploy a Web App on the internet Using:
* AWS lightsail
* Apache2 library
* Postgresql as a database setup

The web app is Store App for mobile and computer accessories

## Server Details
IP Adress: 18.206.38.49

Quich Url: http://18.206.38.49

## Configuration process
* #### Create a new user

        $ sudo adduser grader
        Enter password and name. 

        Give the user a sudo access
        $ sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
        $ sudo nano /etc/sudoers.d/grader
        change ubunto to grader
        
* #### Add Security features

        Create a key pairs for security features
        $ ssh key-gen
        Enter your path ex: /home/ubunto/.ssh/linuxCourse
        $ cat linuxCourse.pub
        copy the output text

        $ su - grader
        $ mkdir .ssh
        $ touch .ssh/authorizd_keys

        $ sudo nano .ssh/authorized_keys
        Paste the copied text in this file

        $ chmod 700 .ssh
        $ chmod 644 .ssh/authorized_keys
        
* #### Install required pachages
    
        $ sudo apt-get update
        $ sudo apt-get install apache2
        $ sudo apt-get install libapache2-mod-wsgi
        $ sudo apt-get install postgresql
        $ sudo apt-get install python-pip
        $ sudo apt-get install python-psycopg2
        $ pip install flask
        $ pip install sqlalchemy
        $ pip install flask_sqlalchemy
        $ pip install oauth2client
        $ pip install requests
        $ pip install httplib2

* #### Create the database
        $ sudo passwd postgres
        Change the user password for postgres
        $ su - postgres
        $ psql
        $ CREATE DATABASE storecatalog;
        $ \q
        
        Change the database password
        $ psql -c "ALTER USER postgres WITH PASSWORD 'ItemStore'" -d storecatalog
        $ exit

        $ cd /var/html
        $ sudo git clone https://github.com/omarelewa21/ItemStore
        $ cd ItemStore/ItemStore
        $ python database.py
        $ python data_creation.py
        
* #### setup configurations
        $ sudo nano /etc/apache2/sites-available/FlaskApp.conf

        <VirtualHost *:80>
    		ServerName mywebsite.com
    		ServerAdmin admin@mywebsite.com
    		WSGIScriptAlias / /var/www/ItemStore/itemstore.wsgi
    		<Directory /var/www/ItemStore/ItemStore/>
    			Order allow,deny
    			Allow from all
    		</Directory>
    		Alias /static /var/www/ItemStore/ItemStore/static
    		<Directory /var/www/ItemStore/ItemStore/static/>
    			Order allow,deny
    			Allow from all
    		</Directory>
    		ErrorLog ${APACHE_LOG_DIR}/error.log
    		LogLevel warn
    		CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        
        
        $ sudo a2ensite FlaskApp
        $ sudo a2dissite 000-default.conf
        
* #### Setup up firewall
        $ sudo ufw default deny outgoing
        $ sudo ufw default allow incoming
        $ sudo ufw allow 2200/tcp
        $ sudo ufw allow www
        $ sudo ufw allow ntp

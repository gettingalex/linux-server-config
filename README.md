# Linux Server Configuration Project

As part of the Full Stack Web Developer Nanodegree with Udacity

## About

The goal of this project is to configure and deploy the catalog flask app created in the previous project with a ubuntu server instance. Here Amazon Lightsail is used.

The website URL is [52.13.215.215.xip.io](52.13.215.215.xip.io), the IP address is 52.13.215.215 and it is on port 2200.

The grader user key is included in the submission comment.


## Configuring and Running the Project

1. Get a server instance
2. Follow the instruction provided to SSH in the server instance.
3. Create a new user called grader & give sudo permission to the new user
4. Secure and configure the server to UTC on port 2200 and e thfirewall to port 2200, 80 and 123
5. Install Apache and mod_wsgi
6. Install and configure PostgreSQL, including a user named catalog with limited permissions to the catalog database
7. Create FlaskApp/
8. Add the virtual host
9. Configure .wsgi file
*Test configuration with simple flask application before cloning app (optional)
10. Install git and clone catalog project repository
11. Modify catalog project python files for deployment
12. Deploy the database
13. Add categories to the database in the terminal with PostgresSQL
14. Update the URL to include domain .xip.io
15. Update configuration on Google developer console
16. Make sure the .git directory is not publicly accessible
17. Restart Apache to apply all changes.



### Get a server instance
1. Get a Amazon Web Services account and create a Amazon Lightsail Linux server instance
2. Open it terminal via the browser and update packages for ubuntu


### Follow the instruction provided to SSH in the server instance.
1. Set the IP of the instance to fix on Amazon Lightsail portal
2. Download the Lightsail default private key to your local Downloads folder
3. Moved it to your local .ssh folder
4. Change the permission to ssh folder and the private key:
```
chmod 700 .ssh
chmod 600 .ssh/private_key_name
```
5. You can now connect with ssh via the terminal.


### Create a new user called grader & give sudo permission to the new user
1. Create new user called grader
```
sudo adduser grader
```
2. To give sudo to grader. Create the grader file with: 
```
sudo nano /etc/sudoers.d/grader
```
3. Add the following to /etc/sudoers.d/grader:
```
grader ALL=(ALL) NOPASSWD:ALL
```
4. Create a new SSH key pair for the grader ssh-keygen user with and add public key to grader user session under .ssh/authorized_keys. Plus, update permission to 
```
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

5. You can now connect to the grader user on the instance with
```
ssh -i ~/.ssh/private_key_name -p 2200 grader@52.13.215.215
```


### Secure and configure the server
1. Change the SSH port from 22 to 2200 in . Plus, update the firewall on AWS Lightsail website to for port 2200
```
/etc/ssh/sshd_config
```
```
sudo service ssh restart
```
2. Disable root login from remote. In /etc/ssh/sshd_config, update it to:
```
PermitRootLogin no
```

3. Disable the password base log in for the remote. In /etc/ssh/sshd_config, update it to:
```
PasswordAuthentication No
```
```
sudo service ssh restart
```
4. Configure the firewall(UFW) to only allows connection for SSH (port 2200), HTTP (port 80), and NTP (port 123).
```
sudo ufw status
```
```
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable 
```
5. Configure to local timezone UTC
```
sudo dpkg-reconfigure tzdata
```


### Install Apache and mod_wsgi
1. Install Apache 
```
sudo apt-get install apache2
```
2. Install python mod_wsgi
```
sudo apt-get install python-setuptools libapache2-mod-wsgi
```
3. Restart Apache
```
sudo service apache2 restart
```

### Install and configure PostgreSQL
1. Install/configure PostgreSQL
```
sudo apt-get install postgresql
```
2. Block remote connections
```
sudo nano /etc/postgresql/9.5/main/pg_hba.conf
```
3. Login as postgres
```
sudo su - postgres
```
4. Start PostgresSQL shell
```
psql
```
5. Create a new database called catalog
```
postgres=# CREATE DATABASE catalog;
```
6. Create a new user in the database called catalog
```
postgres=# CREATE USER catalog;
```
7. Add a password to the user catalog
```
postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
```
8. Give permissions to the catalog database to user catalog
```
postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```
9. Quit postgresSQL and exit user postgres
```
exit
```
10. Install pip: 
```
sudo apt-get install python-pip
```
11. Install psycopg2:

``` sudo apt-get -qqy install postgresql python-psycopg2
```
12. Install the other requirements with pip:
```
sudo pip install requests, sqlalchemy, httplib2
```


### Create FlaskApp/FlaskApp
1. Navigate to the www directory
```
cd /var/www 
```
2. Create the app directory
```
sudo mkdir FlaskApp
```
3. Navigate into the app directory
```
cd FlaskApp
```


### Add the virtual host
1. Install the virtualenv and create one named ven
```
sudo pip install virtualenv 
```
```
sudo virtualenv venv
```
2. Activate the virtual environment and install Flask in the environment
```
source venv/bin/activate
```
```
sudo pip install Flask 
```
You can test your app with sudo python \_\_init_\_.py. Test the main one or just a simple one if you want verify your configuration before you clone your main project

To deactivate the environment, give the following command:
```
deactivate
```
2. Create the FlaskApp.conf file: 
```
sudo nano /etc/apache2/sites-available/FlaskApp.conf
```
3. add the following inside the configuration document
```
<VirtualHost *:80>
	ServerName 52.13.215.215
	ServerAdmin ar_creation@me.com
	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
	<Directory /var/www/FlaskApp/FlaskApp/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/FlaskApp/FlaskApp/static
	<Directory /var/www/FlaskApp/FlaskApp/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
4. Start the virtual host: 
```
sudo a2ensite FlaskApp
```


### The .wsgi file
1. Create the .wsgi file in /var/www/FlaskApp
```
cd /var/www/FlaskApp
sudo nano flaskapp.wsgi 
```
2. Inside flaskapp.wsgi add: 
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'super_secret_key'
```

### Install git and clone catalog project repository
1. Install git
```
sudo apt-get install git
```
2. Navigate to the FlaskApp main folder
```
cd /var/www/Flask/Flask
```
3. Clone the Item Catalog project from GitHub
```
git clone GitHub_URL
```

### Modify catalog project python files for deployment
1. Rename the repository: 
```
sudo mv ./catalog-linux ./FlaskApp
```
2. Navigate to the rename repository: 
```
cd FlaskApp
```
3. Rename application.py to \_\_init_\_.py: 
```
sudo mv application.py __init__.py
```
4. Edit \_\_init_\_.py (formally application.py), database_setup.py and change the create_engine address
```
postgresql://catalog:password@localhost/catalog
```
5. Add path
```
path = os.path.dirname(__file__)
```
6. Update the path to the client secret
```
CLIENT_ID = json.loads(open(path+'/client_secrets.json', 'r').read())['web']['client_id']
```
```
oauth_flow = flow_from_clientsecrets(path+'/client_secrets.json', scope='')
```

### Deploy the database
1. Run the database_setup file: 
```
sudo python database_setup.py
```
2. Restart Apache
```
sudo service apache2 restart
```


### Add categories to the database in the terminal with PostgresSQL
1. Connect via psql, connect to the database and add the categories
```
INSERT INTO category (id, name) VALUES (1, 'Cameras');
INSERT INTO category (id, name) VALUES (2, 'Lenses');
INSERT INTO category (id, name) VALUES (3, 'Tripods');
INSERT INTO category (id, name) VALUES (4, 'Drones');
INSERT INTO category (id, name) VALUES (5, 'Bags');
```
2. Restart Apache
```
sudo service apache2 restart
```

### Update the URL to include domain .xip.io
1. Modify /etc/apache2/sites-available/FlaskApp.conf to:
```
ServerName 52.13.215.215.xip.io
ServerAlias 52.13.215.215.xip.io
```

### Update configuration on Google developer console
1. Update the redirect and origin URL of the Google Sign-in credentials, save and download the update JSON
2. Paste the update content of the client secret JSON file to the client_secrets.json in the server instance.


### Make sure the .git directory is not publicly accessible via the browser
1. Create a .htaccess folder in the root folder of the website on the server instance
2. Write and save the following into it:
```
RedirectMatch 404/\.git
```

### Restart Apache to implement all changes
```
sudo service apache restart
```


### Debugging:
See error log with command: 
```
sudo tail -20 /var/log/apache2/error.log
```

### Software Installed/Tech used:
* [Amazon Lightsail](https://aws.amazon.com/lightsail/)
* [Apache](https://httpd.apache.org)
* [mod_wsgi](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-installation-guide.html)
* [Ubuntu](https://www.ubuntu.com)
* [virtalenv](https://virtualenv.pypa.io/en/latest/)
* [pip](https://pip.pypa.io/en/stable/)
* [Python 2.7](https://www.python.org/download/releases/2.7/)
* [Flask](http://flask.pocoo.org)
* [PostgresSQL](https://www.postgresql.org)
* [psycopg2](http://initd.org/psycopg/)
* [SQLAlchemy](https://www.sqlalchemy.org)
* [httplib2](https://httplib2.readthedocs.io/en/latest/)
* [requests](http://docs.python-requests.org/en/master/)


### References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

https://askubuntu.com/questions/27559/how-do-i-disable-remote-ssh-login-as-root-from-a-server

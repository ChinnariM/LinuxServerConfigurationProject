IP address: 34.227.20.85
Accessible SSH port: 2200.
The complete URL: http://34.227.20.85/
First register in Amazon LightSail, and Create an Instance by following steps
•	Click on create instance and chose Linux/Unix, OS only from there select Ubuntu 16.04LTS.
•	Now choose a type of instance plan ,
•	Then Identify the Instance with name “LinuxServer”.
•	Click on create Instance button, it will take a moment and create an instance.
Generate SSH key Pair
•	Now click on  Accounts in top right corner ,and click on ssh Keys tab , 
•	There will be a default key pair will be there download the private key to your local machine.
•	Move this private key to ~/.ssh folder and rename it to lightsail_key.rsa
•	In the local terminal chmod 600 ~/.ssh/lightsail_key.rsa
•	Now from the terminal try to connect to the Instance created using the key-pair as 
ssh -i ~/.ssh/lightsail_key.rsa ubuntu@34.227.20.85
The authenticity of host 34.227.20.85 (34.227.20.85)' can't be established.
ECDSA key fingerprint is SHA256:7GtFmg5SOrhmzRO7oVURPkhVFMTQwnOJooeulqyvrFw.
Are you sure you want to continue connecting (yes/no)? yes

•	Now user is connected to ubuntu@ip-172-26-1-107:~$ successfully
Now changing the ssh port from 20 to 2200.
•	Now we have to edit the /etc/ssh/sshd_config file , to edit we will use nano command,
sudo nano /etc/ssh/sshd_config
File will be opened in the editor and we can see post 22 change it to 2200 and save.
•	Now restart the SSH: sudo service ssh restart
Now we will configure the filewall (UFW)
•	sudo ufw status : returns status inactive.
•	sudo ufw default deny incoming: deny all the incoming traffic
•	sudo ufw default allow outgoing : allow all outgoing calls
•	sudo ufw allow ssh :allow all ssh connection 
•	sudo ufw allow 2200/tcp : allow incoming tcp on port 2200
•	sudo ufw allow www : allow all http calls
•	sudo ufw allow 123/udp: allow incoming udp on port 123
•	sudo ufw deny 22 :deny incoming request from port 22
•	sudo ufw enable :
 Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
•	now check sudo uw status:
		 
•	Now From the AWS UI click on the Instance , navigate to networking tab,
You can see the default ports defined now update that based on our ufw configuration.
  

•	Now from local terminal try to connect to Ubuntu via port 2200.
•	After changing the firewall in the UI, reboot the instance by clicking reboot button.
•	Now user should be able to connect to the server with port 2200.
•	ssh -i ~/.ssh/lightsail_key.rsa -p 2200 ubuntu@34.227.20.85
Now creating a user grader and giving it sudo access and make sure grader user is logged in based on the key –pair.
•	sudo adduser grader
Adding user `grader' ...
Adding new group `grader' (1001) ...
Adding new user `grader' (1001) with group `grader' ...
Creating home directory `/home/grader' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for grader
Enter the new value, or press ENTER for the default
        Full Name []: Mayuree Chinnari
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y
•	Now giving the user grader sudo permission.
 sudo nano /etc/sudoers.d/grader
now a blank editor  will open up now add grader ALL=(ALL:ALL) ALL to the nano editor Ctrl+X and y to save the file .
•	Open a terminal in your local system then type ssh-keygen , this command will create a key pair .
•	I gave a name grader to store the key pair, move the private key to ~/.ssh folder.
•	Now we have to copy the public key to our virtual machine, now connect to the server with created user grader with su – grader command.
•	Create a new directory .ssh with command mkdir .ssh
•	Now run touch .ssh/authorized_keys
•	Now copy the public key from the local machine and paste in the .ssh/authorized_keys
sudo nano .ssh/authorized_keys
copy the public key click on CTRL+X and Y to save.
•	Now run chmod 700 .ssh
•	Run chmod 644 .ssh/authorized_keys
•	Now Make sure key-based authentication is forced (log in as grader, open the /etc/ssh/sshd_config file, and find the line that says, 'PasswordAuthentication ‘yes', change the 'yes' to 'no';  and set permitrootlogin to 'no' save and exit the file; 
•	run sudo service ssh restart:::: this step is mandatory (don’t forget to execute it)
•	now try to login as grader with key based login
ssh -i ~/.ssh/grader -p 2200 grader@34.227.20.85

Configure the local Time Zone to UTC..
•	Now Run sudo dpkg-reconfigure tzdata, and follow the instructions first select non of the above category another popup cill comeup there select UTC.
•	Make sure time zone configuration done correctly.
Install and configure Apache
•	Run sudo apt-get install apache2 to install Apach
•	Check to make sure it worked by using the public IP of the Amazon Lightsail instance as as a URL in a browser; if Apache is working correctly, a page with the title 'Apache2 Ubuntu Default Page' should load
Install Python mod_wsgi
•	Run sudo apt-get install libapache2-mod-wsgi-py3
•	Make sure mod_wagi is enable using sudo a2enmod wsgi.

Install Postgresql
•	Run sudo apt-get install postgresql.
•	Switch to postgres user sudo su – postgres
•	To open postgresql terminal type psql
•	Create a catalog user with password and give them the ability to create DB.
•	Create the catalog user by running CREATE ROLE catalog WITH LOGIN;
•	Next, give the catalog user the ability to create databases: ALTER ROLE catalog CREATEDB;
•	Finally, give the catalog user a password by running \password ‘123’.
•	Check to make sure the catalog user was created by running \du; a table of sorts will be returned, and it should look like this:
		      List of roles
 Role name |      Attributes                | Member of 
-----------+--------------------------------+-----------
	     catalog   | Create DB                      | {}
             postgres| Superuser, Create role, Create DB, Replication, Bypass RLS| {}
•	Exit psql by running \q
•	Switch back to the ubuntu user by running exit


Linux user catalog and Postgres DB creation.
•	Create a new Linux user called catalog:
i.	run sudo adduser catalog
ii.	enter in a new UNIX password (twice) when prompted
iii.	fill out information for catalog
•	Give the catalog user sudo permissions:
i.	run sudo nano /etc/sudoers.d/catalog
ii.	add the following line below this one: catalog ALL=(ALL:ALL) ALL
iii.	save and close the file
•	While logged in as catalog, create a database called catalog by running createdb catalog
•	Run psql and then run \l to see that the new database has been created
•	Switch back to the ubuntu user by running exit
Install GIT and clone the ItemCatalogProject
•	Run sudo apt-get install git
•	Create a directory called catalog in the /var/www/ directory
•	Change to the catalog directory, and clone the catalog project:
•	sudo git clone https://github.com/ChinnariM/ItemCatalogProject.git catalog
•	catalog Change the ownership of the catalog directory to ubuntu by running (while in /var/www):
•	sudo chown -R ubuntu:ubuntu catalog/
•	Change to the /var/www/catalog/catalog directory
•	Change the name of the application.py file to __init__.py by running mv application.py __init__.py
In __init__.py, find 
app.run(host='0.0.0.0', port=8000)
Change this line to:
app.run()
Add client_secrets.json
•	Create a new project on the Google API Console
•	Create an OAuth Client ID (under the Credentials tab), and make sure to add http://XX.XX.XX.XX  as authorized JavaScript origins
•	add and http:// XX.XX.XX.XX/oauth2callback as authorized redirect URIs
•	Change the client_secrets.json , sudo nano  /var/www/catalog/ catalog by Google  provided  client ID and client secret for the project; download the JSON file, and copy and paste the contents into the client_secrets.json file
•	Add the client ID to  the templates/login.html file in the project directory where client id is specified.
•	Add the complete file path for the client_secrets.json file in the __init__.py file; change it from 'client_secrets.json' to '/var/www/catalog/catalog
client_secrets.json'

Install the virtual environment and dependencies
•	While logged in as grader, install pip: sudo apt-get install python3-pip.
•	Install the virtual environment: sudo apt-get install python-virtualenv
•	Change to the /var/www/catalog/catalog/ directory.
•	Create the virtual environment: sudo virtualenv -p python3 venv3.
•	Change the ownership to grader with: sudo chown -R grader:grader venv3/.
•	Activate the new environment: . venv3/bin/activate.
•	Install the following dependencies:
Pip3 install httplib2
Pip3 install requests
Pip3 install --upgrade oauth2client
Pip3 install sqlalchemy
Pip3 install flask
sudo apt-get install libpq-dev
pip3 install psycopg2
•	Run python3 __init__.py and you should see:
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
•	Deactivate the virtual environment: deactivate.
Set up and enable a virtual host
•	Add the following line in /etc/apache2/mods-enabled/wsgi.conf file to use Python 3.
•	#WSGIPythonPath directory|directory-1:directory-2:...
•	WSGIPythonPath /var/www/catalog/catalog/venv3/lib/python3.5/site-packages
•	Create /etc/apache2/sites-available/catalog.conf and add the following lines to configure the virtual host:
<VirtualHost *:80>
    ServerName 34.227.20.85
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
    	Order allow,deny
  	  Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
  	  Order allow,deny
  	  Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

•	Enable virtual host: sudo a2ensite catalog. The following prompt will be returned:
		
Enabling site catalog.
To activate the new configuration, you need to run:
  service apache2 reload
•	Reload Apache: sudo service apache2 reload.
 Set up the Flask application
•	Create /var/www/catalog/catalog.wsgi file add the following lines:
activate_this = '/var/www/catalog/catalog/venv3/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/catalog/")
sys.path.insert(1, "/var/www/catalog/")

from catalog import app as application
application.secret_key =”testing linux server”
•	Restart Apache: sudo service apache2 restart
 Set up the database schema and populate the database
•	Add the these two lines at the beginning of the file lotsofitems.py.
import sys
sys.path.insert(0, "/var/www/catalog/catalog/venv3/lib/python3.5/site-packages") 
•	From the /var/www/catalog/catalog/ directory, activate the virtual environment: . venv3/bin/activate.
•	Run: python lotsofitems.py.
•	Deactivate the virtual environment: deactivate.
Disable the default Apache site
•	Disable the default Apache site: sudo a2dissite 000-default.conf. The following prompt will be returned:
Site 000-default disabled.
To activate the new configuration, you need to run:
  service apache2 reload
•	Reload Apache: sudo service apache2 reload.
 Launch the Web Application
•	Change the ownership of the project directories: sudo chown -R www-data:www-data catalog/.
•	Restart Apache again: sudo service apache2 restart.
•	Open your browser to http://34.227.20.85 or http://ec2-34-227-20-85.us-east-1a.compute.amazonaws.com
Resources:
1.	https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
2.	https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
3.	GitHub Repositories: adityamehra/udacity-linux-server-configuration

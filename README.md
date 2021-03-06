# test

###################### webserver Settings###############
# Change this file in /project/settings.py

###############################################settings.py Changes########################
# Keep these settings fixed
# * for allowed hosts

MEDIA_ROOT = os.path.join(BASE_DIR, 'static')
MEDIA_URL = '/media/'
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, "static/")


#################################### wsgi.py Keep defaults################################
# Make Changes according to your requirement
# Change this file in /project/wsgi.py

"""
WSGI config for myproject project.

It exposes the WSGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/2.0/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project.settings")

application = get_wsgi_application()


###############################Apache_settings############################################

sudo vi /etc/apache2/sites-available/test.conf

# Make Changes according to your requirement
 
<VirtualHost *:80>
	# The ServerName directive sets the request scheme, hostname and port that
	# the server uses to identify itself. This is used when creating
	# redirection URLs. In the context of virtual hosts, the ServerName
	# specifies what hostname must appear in the request's Host: header to
	# match this virtual host. For the default virtual host (this file) this
	# value is not decisive as it is used as a last resort host regardless.
	# However, you must set it for any further virtual host explicitly.
	#ServerName www.example.com

	ServerAdmin webmaster@localhost
	#DocumentRoot /var/www/html
  DocumentRoot /home/docker/project

	# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
	# error, crit, alert, emerg.
	# It is also possible to configure the loglevel for particular
	# modules, e.g.
	#LogLevel info ssl:warn

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined

	# For most configuration files from conf-available/, which are
	# enabled or disabled at a global level, it is possible to
	# include a line for only one particular virtual host. For example the
	# following line enables the CGI configuration for this host only
	# after it has been globally disabled with "a2disconf".
	#Include conf-available/serve-cgi-bin.conf
    
    Alias /static /home/docker/project/static
    <Directory /home/docker/project/static>
        Require all granted
    </Directory>

    <Directory /home/docker/project/>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess project python-path=/home/docker/project python-home=/home/docker/project/env
    WSGIProcessGroup project
    WSGIScriptAlias / /home/docker/project/wsgi.py

</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet


 
########################################################################
################## For mysql database
virtualenv projectenv
source projectenv/bin/activate
pip install -r requirements.txt

cd /project                ######### Change path accordingly
pip install -r requirements.txt 
./manage.py makemigrations
./manage.py migrate
./manage.py collectstatic
./manage.py runserver 0.0.0.0:8000         # Check application is working or not

##################### for default sqlite databse#################
chmod 664 ./project/db.sqlite3
sudo chown :www-data ./project/db.sqlite3                       ###### Skip this Section for mysql database
sudo chown :www-data ./project
################################################################

sudo apt-get update

sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3

sudo a2dissite 000-default.conf

sudo a2ensite   test.conf

sudo chown :www-data ./project

sudo service apache2 restart

# Deploy-Flaks-App
# Deploy Flaks Nginx


### Preparation
 STEP 1 : sudo apt-get install python3-pip python3-dev nginx uwsgi uwsgi-plugin-python3

 STEP 2 : Create virtualenv wit python version

 STEP 3 : activate env

 STEP 4 : clone your project

 STEP 5 : install your requirements.txt

 STEP 6 : create wsgi.py in your project with this code

    from run import app
    if __name__=="__main__":
	    app.run()




### Deploying
 STEP 1 : in directory `/etc/uwsgi/apps-available` create `projectname.ini` file with this config

    [uwsgi]
    vhost = true
    #http-socket = 0.0.0.0:5001
    plugin = python3
    socket = /tmp/projectname.sock
    master = true
    enable-threads = true
    processes = 5
    #cpu-affinity = 1
    #prio = 2
    max-requests = 3000
    stats = /tmp/projectname.stats
    virtualenv = /home/username/www/venv
    chdir = /home/username/www/projectname
    touch-reload = /home/username/www/projectname/reload
    module=wsgi:app
    uid = username
    gid = username
    chmod-socket = 777
    chown-socket = username
    buffer-size = 65536
    env = PG_USERNAME= DBusername
    env = PG_PASSWORD= DBpassword
    env = LOG_PATH=/home/username/www/log/


STEP 2 : link uwsgi config 

`sudo ln -s /etc/uwsgi/apps-available/projectname.ini /etc/uwsgi/apps-ena#bled`

STEP 3 : in directory /etc/nginx/sites-available create projectname file with this config

    server {
        listen 80;
        server_name server_domain_or_IP;

        location / {
            include uwsgi_params;
            uwsgi_pass unix:///tmp/projectname.sock;
       # }
    }

STEP 4 : link nginx config 

`sudo ln -s /etc/nginx/sites-available/projectname /etc/nginx/sites-enabled`


### Note 1 :
can chek uwsgi log with this command 

`sudo tailf /var/log/uwsgi/app/projectname.log`

### Note 2 :
can chek nginx log with this command 

`sudo tailf /var/log/nginx/error.log`

### Note 3 :
postgresql

`sudo su - postgres`

`psql`

`CREATE DATABASE databasename;`

`CREATE USER dbusername WITH PASSWORD 'dbpassword';`

`ALTER ROLE dbusername SET client_encoding TO 'utf8';`

`ALTER ROLE dbusername SET default_transaction_isolation TO 'read committed';`

`ALTER ROLE dbusername SET timezone TO 'UTC';`

`GRANT ALL PRIVILEGES ON DATABASE databasename TO dbusername;`






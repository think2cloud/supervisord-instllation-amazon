
### Installing Supervisod

```
[clado]# yum install python2 python2-pip
[clado]# pip2 install flask
[clado]# pip2 install supervisor
[clado]# pip2 install  gunicorn
[clado]# mkdir /etc/supervisord/
[clado]# mkdir /etc/supervisord/conf.d
```


### Creating /etc/supervisord/supervisord.conf

```
echo_supervisord_conf > /etc/supervisord/supervisord.conf
```

### Creating Shortcuts to /etc/

```
 ln -s /etc/supervisord/supervisord.conf /etc/supervisord.conf
```

### Adding the include directory to hold extra service scripts
```
[clado]# echo -e "[include]\nfiles = conf.d/*.conf" >> /etc/supervisord/supervisord.conf
```

### Creating systemd supervisord.service unit

```
[clado]# vim /usr/lib/systemd/system/supervisord.service
```

```
[Unit]
Description=supervisord - Supervisor process control system for UNIX
Documentation=http://supervisord.org
After=network.target

[Service]
Type=forking
ExecStart=/bin/supervisord -c /etc/supervisord/supervisord.conf
ExecReload=/bin/supervisorctl reload
ExecStop=/bin/supervisorctl shutdown
User=root

[Install]
WantedBy=multi-user.target
```

### Creating Sample Flask Application

```
[clado]# mkdir /var/flask
```
```
[clado]# vim /var/flask/cladoapp.py
```
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<h1><center>Simple Flask Application</center></h1>'
```

### Creating Supervisord script for gunicorn to run cladoapp.py

```
[clado]# /etc/supervisord/conf.d/cladoapp.conf
```

```
[program:cladoapp]
directory=/var/flask
command=gunicorn cladoapp:app -b 0.0.0.0:8080
autostart=true
autorestart=true
stderr_logfile=/var/log/cladoapp/cladoapp.err.log
stdout_logfile=/var/log/cladoapp/cladoapp.out.log                                  ```
```

```
[clado]# mkdir  /var/log/cladoapp
```

### Starting Supervisord Service unit 

```
[clado]# service supervisord restart
```

### Checking Application Stattus

```
[clado]# supervisorctl 
cladoapp         RUNNING   pid 360, uptime 0:00:02
```

### Accessing Application

```
http://ip-of-the-instance:8080
```
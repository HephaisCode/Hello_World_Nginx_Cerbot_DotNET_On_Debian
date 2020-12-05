# "Hello World!" with Nginx with DotNET and Cerbot on Debian 10

[![OS badge](https://img.shields.io/badge/OS-Debian-red.svg)](https://www.debian.org)
[![Server badge](https://img.shields.io/badge/Server-Nginx-blue.svg)](https://www.nginx.com)
[![SSL badge](https://img.shields.io/badge/SSL-certbot-blue.svg)](https://certbot.eff.org)
[![Format badge](https://img.shields.io/badge/Format-HTML-green.svg)](https://lyty.dev/html/index.html)

## Objectif 

Create Web Page for **hello-world.hephaiscode.com** with Nginx and HTML File. Access by URL  http://hello-world.hephaiscode.com .

## You need

- Server on Debian (linux distribution) with root access
- DN (Domain Name) point on Server IP (example hello-world.hephaiscode.com on 51.83.45.10)
- Terminal/console to enter instruction

## Parameters

We use :
 - hello-world.hephaiscode.com for domain name of our web page
 - 51.83.45.10 the IP address of our server computer
 - Create an user hephaistos with password 'Gkh23hglxVd47ShG43jh3h' (change the password!)
 
 
## Connect to server 

Open terminal or console and go to admin your server.

```
ssh-keygen -R 51.83.45.10
ssh -l root 51.83.45.10 
```

And enter your root password 

## Update your server

Always to be update.

```
apt-get update
apt-get -y upgrade
apt-get -y dist-upgrade

```

## Install Nginx

Install Nginx as WebServer to communicate with your server at **hello-world.hephaiscode.com**

```
apt-get -y install nginx
apt-get -y install logrotate
systemctl start nginx
```

Configure Default Nginx by rewrite index.html

```
chgrp -R www-data /var/www/html/
chmod 750 /var/www/html/

rm /var/www/html/index.html
echo "<html><body>Are you lost? Ok, I'll help you, you're in front of a screen...</body></html>" > /var/www/html/index.html

DEFAULTNGINXCONFIG=/etc/nginx/sites-available/default
rm ${DEFAULTNGINXCONFIG}

```
Configure Default Nginx by Error 444

```
echo 'server {' >> ${DEFAULTNGINXCONFIG}
echo '    listen   80 default_server;' >> ${DEFAULTNGINXCONFIG}
echo '    # listen [::]:80 default_server deferred;' >> ${DEFAULTNGINXCONFIG}
echo '    return   444;' >> ${DEFAULTNGINXCONFIG}
echo '}' >> ${DEFAULTNGINXCONFIG}

ln -s ${DEFAULTNGINXCONFIG} /etc/nginx/sites-enabled/

systemctl restart nginx



```

Test Nginx 

Open browser and go to page http://51.83.45.10/

## Install DotNET

Install DotNET with Nginx

```
wget https://packages.microsoft.com/config/debian/10/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
dpkg -i packages-microsoft-prod.deb

apt-get update
apt-get install -y apt-transport-https
apt-get install -y dotnet-sdk-5.0
apt-get install -y aspnetcore-runtime-5.0
apt-get install -y dotnet-runtime-5.0

```

 ## Define our parameters
 
 ```
 MYUSER=hephaistos
 MYPASSWORD=Gkh23hglxVd47ShG43jh3h
 MYDOMAINNAME=hello-world.hephaiscode.com
 MYEMAIL=hello-world@hephaiscode.com
 MYWEBFOLDER=WebSite
 ```
 
 ## Create user ${MYUSER}
 
 ```
useradd --shell /bin/false ${MYUSER}
echo ${MYUSER}:${MYPASSWORD} | chpasswd
mkdir /home/${MYUSER}
chown root /home/${MYUSER}
chmod go-w /home/${MYUSER}

```

Add directory

```
mkdir /home/${MYUSER}/${MYWEBFOLDER}

rm /home/${MYUSER}/${MYWEBFOLDER}/index.html
echo '<html><body>Hello World!</body></html>' >> /home/${MYUSER}/${MYWEBFOLDER}/index.html

chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER}
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}
```

## Install Domain Name

Create the host parameters for Apache and our domains **hello-world.hephaiscode.com**

```


MYNGINXCONFIG=/etc/nginx/sites-available/${MYDOMAINNAME}
rm ${MYNGINXCONFIG}
echo 'server {' >> ${MYNGINXCONFIG}
echo " listen 80;" >> ${MYNGINXCONFIG}
echo " listen [::]:80;" >> ${MYNGINXCONFIG}
echo " root /home/${MYUSER}/${MYWEBFOLDER};" >> ${MYNGINXCONFIG}
echo " index index.html index.htm index.nginx-debian.html;" >> ${MYNGINXCONFIG}
echo " server_name ${MYDOMAINNAME};" >> ${MYNGINXCONFIG}
echo ' location / {' >> ${MYNGINXCONFIG}
echo '  proxy_pass         http://localhost:5000;' >> ${MYNGINXCONFIG}
echo '  proxy_http_version 1.1;' >> ${MYNGINXCONFIG}
echo '  proxy_set_header   Upgrade $http_upgrade;' >> ${MYNGINXCONFIG}
echo '  proxy_set_header   Connection keep-alive;' >> ${MYNGINXCONFIG}
echo '  proxy_set_header   Host $host;' >> ${MYNGINXCONFIG}
echo '  proxy_cache_bypass $http_upgrade;' >> ${MYNGINXCONFIG}
echo '  proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;' >> ${MYNGINXCONFIG}
echo '  proxy_set_header   X-Forwarded-Proto $scheme;' >> ${MYNGINXCONFIG}
echo ' }' >> ${MYNGINXCONFIG}
echo '}' >> ${MYNGINXCONFIG}

ln -s ${MYNGINXCONFIG} /etc/nginx/sites-enabled/

systemctl restart nginx

```


Open browser and go to page http://hello-world.hephaiscode.com 

## Add ssl certificat by Certbot

```
apt-get -y install certbot python-certbot-nginx
```

Then you can certifiate your website 

```
certbot --agree-tos -n --no-eff-email --nginx --redirect --email ${MYEMAIL} -d ${MYDOMAINNAME}

systemctl restart nginx

```

## Hello World Test

Open browser and go to page http://hello-world.hephaiscode.com 

Open browser and go to page https://hello-world.hephaiscode.com (certificat is valided by certbot)

## Hello World Success

![Success](https://img.shields.io/badge/Hello%20World-OK-Green.svg)

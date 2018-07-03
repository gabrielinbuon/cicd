## Continuous Integration and Continuous Deployment Pipeline

CI/CD Implementation Technical Setup Guide


What we will be using
- AWS EC2 Ubuntu 16 (3 instances for Jenkins, Staging and Prod servers)
- PHP 7.2
- Nginx
- Jenkins
- Bitbucket
- Git
- Composer

---------------------------------------------------------------------------------------------------------------
Jenkins Installation:

first update/upgrade your packages repo
```sh
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
```

Install Composer and Git
```sh
$ sudo apt-get install composer -y
$ sudo apt-get install git -y
```

Instll OpenJDK 8:
```sh
$ sudo apt-get install openjdk-8-jdk
```

Init Jenkins Installation
```sh
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
$ sudo apt-get update
$ sudo apt-get install jenkins
```

After successful installation you can access your new Jenkins using your server IP: 'YOUR-IP:8080'

> Note: 
> Accessing Jenkins the first time will ask your administrator password to start the setup.
> You can find from your Jenkins installation directory.
> Example: sudo nano /var/lib/jenkins/secrets/initialAdminPassword

Jenkins First Time Access Page - Setup setps:
***just click 'continue' for everything :D***
> Note: provide your username and password to be in 'create first admin user' page

Jenkins document can be found [here](https://jenkins.io/doc/book/installing)

System Requirement:

| Minimum | Recommended|
|  -- |  -- |
|  256 MB of RAM |  1 GB+ of RAM |
|  1 GB of drive space  |  50 GB+ of drive space |

-----------------------------------------------------------------------------------------------------



### Stage & Prod LEMP Setup:

packages update for your distro
```sh
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
```

Install Composer and Git
```sh
$ sudo apt-get install composer -y
$ sudo apt-get install git -y
```

PHP Installation:
As I prefer to use PHP7.2 I will be using another repo to pull from.

Update python package
```sh
$ sudo apt-get install python-software-properties
```
Add new php repo into our disto and start installation process
```sh
$ sudo add-apt-repository ppa:ondrej/php -y
$ sudo apt-get update
$ sudo apt-get install PHP7.2-fpm -y
$ sudo apt-get install php7.2-curl php7.2-bcmath php7.2-json php7.2-mbstring php7.2-tidy php7.2-soap php7.2-mysql php7.2-xml php7.2-xmlrpc -y
```

After running the commands above, apache2 will auto installed. We now have to remove Apache2 as we prefer using Nginx
```sh
$ sudo service apache2 stop
$ sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
$ where apache2 (delete the output folder using : sudo rm -rf /usr/bin/apache2)
```

Nginx Installation:
```sh
$ sudo apt-get install nginx
```
Now, let us hook php-fpm in nginx
```sh
$ sudo nano /etc/nginx/sites-available/default 
```
Location section should look something like this or copy and paste it into your default config file.
```sh
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    include fastcgi_params;
}
```
Also make sure Nginx is looking at .php files as a priority. Should look something like this: 
```sh
# Add index.php to the list if you are using PHP
index index.php index.html index.htm index.nginx-debian.html;
```

Restart your nginx to finish the LEMP setup process.
```sh
$ sudo service nginx restart
```

To make sure everything is working fine, you need to create index.php in nginx html folder with the content of phpinfo()
```sh
$ sudo nano /var/www/html/index.php

content should look like this
<?php
phpinfo();
```
save it and visit your domain/ip. You will see phpinfo page if everything works fine.

---------------------------------------------------------------------------------

#### Jenkins & Bitbucket Configurations/Settings:

Login in your Jenkins
- Go to 'Manage Jenkins'
-- Go to 'Manage Plugins'
-- Go to 'Available' and search for Bitbucket, click install

Generate SSH key in Jenkins server
switch to jenkins user account
```sh
$ sudo su jenkins
$ ssh-keygen
$ cat /var/lib/jenkins/.ssh/id_rsa.pub (copy the content to use later)
```
Add the pub key in Stage/Prod (be in root user)
```sh
$ nano .ssh/authorized_keys
```

Bitbucket:
Got to Repository
- Settings
-- Deployment keys
-- Add Key: "Your copied key here again in the key input field"

Jenkins: create new Job
- git your project a name
- free style project
- select git from 'Source code Management'
-- add Repository URL
- Build Triggers
	- Select: Build when a change is pushed to BitBucket
- Add Build Step:
	- Execute Shell

	Shell commands are:
	rsync -vrzhe "ssh -o StrictHostKeyChecking=no" . root@13.232.22.133:/var/www/html
	ssh root@13.232.22.133 <<EOF
	  cd /var/www/html
	EOF

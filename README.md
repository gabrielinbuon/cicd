# cicd
CI/CD Implementation Technical Setup Guide

Jenkins Installation:

first update/upgrade your packages repo
- sudo apt-get update -y
- sudo apt-get upgrade -y

Instll OpenJDK 8:
- sudo apt-get install openjdk-8-jdk

wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins


After successful installation you can access your new Jenkins using your 'YOUR-IP:8080'
Note: Accessing Jenkins the first time will ask up administrator password to start the setup, use the command and copy the password: sudo nano /var/lib/jenkins/secrets/initialAdminPassword)

Setup setps:
just click 'continue' for everything :)
Note: provide your username and password to be in 'create first admin user' page


Jenkins document
Documentation: https://jenkins.io/doc/book/installing/

-----------------------------------------------------

System Requirement:

Minimum hardware requirements:
	256 MB of RAM
	1 GB of drive space (although 10 GB is a recommended minimum if running Jenkins as a Docker container)

Recommended hardware configuration for a small team:
	1 GB+ of RAM
	50 GB+ of drive space

---------------------------------------



Stage & Prod LEMP Setup:
- sudo apt-get update -y
- sudo apt-get upgrade -y

PHP:
As I prefer to use PHP7.2 (latest) I will be using another repo to pull from.

update python package
- sudo apt-get install python-software-properties
- sudo add-apt-repository ppa:ondrej/php -y
- sudo apt-get update
- sudo apt-get install PHP7.2-fpm -y
- install extsions:  sudo apt-get install php7.2-curl php7.2-bcmath php7.2-json php7.2-mbstring php7.2-tidy php7.2-soap php7.2-mysql php7.2-xml php7.2-xmlrpc -y

After running the commands above, apache2 will auto installed. We now have to remove Apache2 as we prefer using Nginx
- sudo service apache2 stop
- sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
- where apache2 (delete the output folder using : sudo rm -rf /usr/bin/apache2)

Nginx: 
- sudo apt-get install nginx
Now, let us hook php-fpm in nginx
- sudo nano /etc/nginx/sites-available/default 

location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    include fastcgi_params;
}

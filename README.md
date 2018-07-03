## Continuous Integration and Continuous Deployment Pipeline

CI/CD Implementation Technical Setup Guide


What we will be using
- AWS EC2 Ubuntu 16 (3 instances for Jenkins, Staging and Prod servers)
- PHP 7.2
- Nginx
- Jenkins
- Bitbucket


Jenkins Installation:

first update/upgrade your packages repo
```sh
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
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

---------------------------------------



### Stage & Prod LEMP Setup:

packages update for your distro
```sh
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
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

Location section should look something like this or copy and paste it into your default config file.
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    include fastcgi_params;
}
```

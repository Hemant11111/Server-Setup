### *Server Setup From Scratch*

* *[Installations](#installations)*

* *[Users and groups on Ubuntu.](#users-and-groups-on-ubuntu)*

* *[Deploy code.](#deploy-code)*

* *[Users and groups on Ubuntu (Inside Project Directory).](#users-and-groups-on-ubuntu-inside-project-directory)*

* *[Virtual host config.](#virtual-host-config)*

* *[Project Setup.](#project-setup)*










#### Installations:

##### Install PHP:
```
sudo LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install apache2
sudo apt-get install gzip
sudo apt-get install php7.1
sudo apt install zip unzip php7.1-zip
```

##### Fixing the environment issue in ubuntu
```
sudo nano /etc/environment

LC_ALL=en_US.UTF-8
LANG=en_US.UTF-8
```

```
sudo a2enmod rewrite
sudo service apache2 restart
```

##### Install Composer:
```
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

##### Install Curl:
```
sudo apt-get install php7.1-curl
sudo phpenmod curl
```

##### Install xmlrpc:
```
sudo apt-get install php7.1-xmlrpc
sudo phpenmod xmlrpc
```

##### Install mbstring:
```
sudo apt-get install php7.1-mbstring
sudo phpenmod mbstring
```

##### Install dom:
```
sudo apt-get install php7.1-dom
sudo phpenmod dom
```

##### Install gd:
```
sudo apt-get install php7.1-gd
sudo phpenmod gd
```

##### Install mysql:
```
sudo apt-get install php7.1-mysql
```

##### Install memcached:
```
sudo apt-get install php7.1-memcached
sudo phpenmod memcached
```

##### Install ImageMagick:
```
sudo apt-get install imagemagick
sudo apt-get install php7.1-imagick
sudo phpenmod imagick
```


##### Restart Apache:

```
sudo service apache2 restart
```









##### Users and groups on Ubuntu:
```
// adds a group
sudo groupadd htmlowners

// adds user to group
sudo usermod -a -G htmlowners ubuntu
sudo usermod -a -G htmlowners www-data

// change user and group of directory
sudo chown -R ubuntu:htmlowners api.test.com

sudo find api.test.com -type d -exec chmod 775 {} \;
sudo find api.test.com -type f -exec chmod 664 {} \;
```






##### Deploy Code:
- *PUSH FROM [BEANSTALK](https://beanstalkapp.com/).*
  ```
  Add Environment-wide deployment key. By adding this public key to the physical servers you're deploying to, all deployments in this environment will be able to deploy there through SSH/SFTP.
                                       
  nano ~/.ssh/authorized_keys
  
  copy key to authorized_keys file.
 
  ``` 

- *PULL FROM [GITHUB](https://github.com/) / [BITBUCKET](https://bitbucket.org/).*
  ```
  git clone https://www.repo.url
  ``` 






###### Users and Groups on Ubuntu (Inside Project Directory):

```
// set setgid bit of directory
sudo chmod -R g+s storage
sudo chmod -R g+s bootstrap/cache

sudo chmod -R u+s storage
sudo chmod -R u+s bootstrap/cache
 
sudo find storage -type d -exec chmod 775 {} \;
sudo find storage -type f -exec chmod 664 {} \;


sudo find bootstrap/cache -type d -exec chmod 775 {} \;
sudo find bootstrap/cache -type f -exec chmod 664 {} \;

sudo chmod 777 storage
// fix.

```










##### Virtual host config:

##### #*Default Server Setup*

```
sudo nano /etc/apache2/sites-available/000-default.conf

        <Directory /var/www/api.test.com/public>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/api.test.com/public

*******************************************************

sudo nano /etc/apache2/sites-available/api.test.com.conf

<VirtualHost *:80>
        <Directory /var/www/api.test.com/public>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>
        
        ServerName api.test.com
        ServerAdmin HemantSaini95689@gmail.com
        DocumentRoot /var/www/api.test.com/public

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>



sudo a2ensite api.test.com.conf
sudo service apache2 restart
```







##### Project Setup:
```
cp .env.example .env
composer install
php artisan key:generate
php artisan jwt:secret
```

##### Umash Apache:

```
Setting the umask on apache restart
/etc/apache2/envvars

umask 002
```



##### Create swap:
```
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

sudo nano /etc/fstab
/swapfile   none    swap    sw    0   0

sudo sysctl vm.swappiness=10
sudo sysctl vm.vfs_cache_pressure=50


sudo nano /etc/sysctl.conf
vm.swappiness=10
vm.vfs_cache_pressure = 50
```

##### Install supervisor:
```

sudo apt-get install supervisor

sudo nano /etc/supervisor/conf.d/laravel-worker.conf

[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/newapi.stayduvet.com/artisan queue:work --sleep=60 --tries=6 --daemon
autostart=true
autorestart=true
user=ubuntu
numprocs=2
redirect_stderr=true
stdout_logfile=/var/www/newapi.stayduvet.com/worker.log


sudo systemctl start supervisor sudo systemctl enable supervisor sudo supervisorctl reload

sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start laravel-worker:*
```




##### Setup cron:
```
* * * * * sudo php /var/www/test-api.stayduvet.com/artisan schedule:run 1>> /dev/null 2>&1
```



##### END OF SERVER SETUP 

```
sudo supervisorctl start laravel-worker:*





sudo rm -rf vendor

then
composer install

it gave error "The zip extension and unzip command are both missing."

So I run:
sudo apt install zip unzip php7.1-zip

and then:
composer install





————————————————————————————————————————————————————
sudo supervisorctl => opens terminal for supervisor


-----------------------------------------------------
Beanstalk setup:
add beanstalk ssh key to ubuntu authorized_keys file
nano ~/.ssh/authorized_keys

```
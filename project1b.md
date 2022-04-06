LAMP STACK WEBSITE on AWS Cloud

> Creat an account in AWS cloud, create an Ubuntu ec2 instance and Connect to the instance.


## Install Apache and update the firewall
```Bash
sudo apt update
sudo apt install apache2
sudo systemctl status apache2

   ```
> We obtain the following results. which shows our webserver is up and running.

![](https://user-images.githubusercontent.com/64647166/161851352-245fb913-d57f-4009-a477-800d3473d402.png)

> Edit the ec2 instance security group and open TCP port 80 for our webserver to be able to recieve traffic locally and over the internet.

![image](https://user-images.githubusercontent.com/64647166/161853454-5133144a-0b63-4297-a2c7-c110fbafb480.png)

> Access the webserver locally and over the internet respectively.
```Bash
curl http://172.31.20.148
http://54.227.9.232:80
```
> We get the following result when we access over the internet.

![image](https://user-images.githubusercontent.com/64647166/161856428-f9ad11d8-9e7f-4fdf-a2c9-2a85f43eb46c.png)

## Install MYSQL Software
> We run the following command to instaall MYSQL software and configure its security, then connect to it as the administrative user.
```Bash
sudo apt install mysql-server
sudo mysql_secure_installation
sudo mysql
```
> We get the following results

![image](https://user-images.githubusercontent.com/64647166/161858296-319b6941-15a5-4b56-9800-bab6b6c3c9f0.png)

## Install PHP
> Run the following command to install the php package, php-mysql, and libapache2-mod-php. Then check the PHP version
```Bash
sudo apt install php libapache2-mod-php php-mysql
php -v
```

## Create a virtual Host for the webserver

> we create a custom directory called projectlamp to serve our website and change the ownership of  the directory to the current system user.

```Bash
sudo mkdir /var/www/projectlamp
sudo chown -R $USER:$USER /var/www/projectlamp
```
> Creat abd edit a new configuration file for our custom domain

```Bash
sudo vi /etc/apache2/sites-available/projectlamp.conf
```
![image](https://user-images.githubusercontent.com/64647166/161861658-c6a2041a-52b3-4d6a-bcb2-788f99102e5f.png)

> Enable the new virtual host, verify your configuration file does not have any error, and disable the default webserver using the following commands.
```Bash
sudo a2ensite projectlamp
sudo a2dissite 000-default
sudo apache2ctl configtest
sudo systemctl reload apache2
```

> Create an index.html file at the web root to test the virtual host.

```Bash
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
> Try to access your website over the internet.

``` 
http://54.227.9.232:80
```
> We get the following result

![image](https://user-images.githubusercontent.com/64647166/161863554-b057bf60-60fe-43e2-bf5b-8ea996f37460.png)

## Enable PHP on the website
> Edit the /etc/apache2/mods-enabled/dir.conf to modify the default DirectoryIndex such that the index.php file will take precedence over the index.html.

```Bash
sudo vim /etc/apache2/mods-enabled/dir.conf
```
![image](https://user-images.githubusercontent.com/64647166/161864711-1f2a138f-a6b7-45e0-bae4-fc82cbbef329.png)

> Reload apache for changes to take effect and create a new index.php file
```Bash
sudo systemctl reload apache2
vim /var/www/projectlamp/index.php
```

> Insert the following sample php code into the index.php file

```php
<?php
phpinfo();
``` 
> Access the webserver using the public ipv4. We get the following result

![image](https://user-images.githubusercontent.com/64647166/161865969-50e29605-0b9e-4ab9-9e78-8f649fa9c0d1.png)


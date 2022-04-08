# **WEBSITE LAUNCHING IN AWS USING A LEMP STACK TECHNOLOGY**
### *Prerequisite*
> Creat an account in AWS cloud, create an Ubuntu ec2 instance and Connect to the instance.

1. ## INSTALL NGINX WEBSERVER
> Update your ubuntu server, then install Nginx webserver, and verify that your webserver is up and running using the following commands respectively. 
```Bash
sudo apt update
sudo apt install nginx
sudo systemctl status nginx
```
> The following results shows your webserver is up and running as a service.

![image](https://user-images.githubusercontent.com/64647166/161973695-35d03c3d-facd-4ed1-9543-91ceae1abb88.png)

> Edit the ec2 instance security group and open TCP port 80 for our webserver to be able to recieve traffic locally and over the internet.

![image](https://user-images.githubusercontent.com/64647166/161974233-7e3a6477-b338-476b-b3e9-393c814deeb0.png)
> We can access the webserver locally and over the internet using the following command and url respectively.
```Bash
curl http://172.31.89.234
http://3.83.109.214:80
```
> We get the following result when we acces the website over the internet

![image](https://user-images.githubusercontent.com/64647166/161975815-98a0f155-8812-427c-ad9f-c7d04d88dc0c.png)

2. ## Install MYSQL Software
> We run the following command to instaall MYSQL software and configure its security.
```Bash
sudo apt install mysql-server
sudo mysql_secure_installation
sudo mysql
```
> Try to connect to your MYSQL database as the administrative user using the following command.
```Bash
sudo mysql
```
> We get the following result

![image](https://user-images.githubusercontent.com/64647166/161979972-a7e22b94-cc8f-48f0-a567-98f835669536.png)

3. ## INSTALL PHP
> Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter and the web server. So we need to install this program and also install the php-mysql for PHP to communicate with MYSQL database.
```Bash
sudo apt install php-fpm php-mysql
```

4. ## CONFIGURE NGINX TO USE PHP PROCESSOR
> Creat a custom web root directory to host a new domain
```Bash
sudo mkdir /var/www/projectLEMP
```
> change ownership of the directory using the $USER environment variable to reference your current system user
```Bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```
> Create a new configuration file for your new domain in Nginx’s sites-available directory and edit the configurations.

```Bash
sudo nano /etc/nginx/sites-available/projectLEM
```
> Activate your configuration by linking to the config file from Nginx’s sites-enabled directory. This will tell Nginx to use the configuration next time it is reloaded
```Bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
> Test your configuration for syntax error using the following command.
```Bash
sudo nginx -t
```
> You get the following result

![image](https://user-images.githubusercontent.com/64647166/162253759-644abe3b-e264-456c-9023-99cc537e937b.png)

> Disable default Nginx host that is currently configured to listen on port 80 and then reload Nginx for the changes to take place.
```Bash
sudo unlink /etc/nginx/sites-enabled/default
sudo systemctl reload nginx
```
> Create an index.html file in the /var/www/projectLEMP location so that you can test that your new server block works as expected.
```Bash
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
> Try to access your website from your browser using the following command.
```Bash
http://<Public-IP-Address>:80
```
> You should get the following result

![image](https://user-images.githubusercontent.com/64647166/162257556-cb842fd2-ace7-40b7-ae08-7d983eedda90.png)

5. ## TEST PHP WITH NGINX
> create a test PHP file in your document root and insert some test php code.
```Bash
sudo nano /var/www/projectLEMP/info.php
```
```PHP
<?php
phpinfo();
```
> you can now access your website using the following url
http://`server_domain_or_IP`/info.php

> You should get the folowing results
![image](https://user-images.githubusercontent.com/64647166/162259761-80ef17e4-cb50-497b-80b1-b12d3b9a25a9.png)

6. ## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP
> Connect to MYSQL console as the root user and create a new database
```Bash
sudo mysql
mysql> CREATE DATABASE `example_database`;
```
>Create a new user called e.g 'example_user', using mysql_native_password as default authentication method, and then define a password e.g 'password' for the user. Grant this user full permission on your example_database.
```MYSQL
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL PRIVILEGES ON example_database.* TO 'example_user'@'%';
```
> Exit the mysql console, lock back in as our new user and try to access the database.
```MYSQL
exit
mysql -u example_user -p
SHOW DATABASES;
```
> we get the following result
![image](https://user-images.githubusercontent.com/64647166/162272950-9819bae9-e473-4e1f-950f-e2b72d5e1732.png)

> Create a test table named todo_list. From the MySQL console by issuing the following commands.
```
CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );
```
> Insert a few rows of content in the test table and confirm that the data was successfully saved to your table

```
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
mysql>  SELECT * FROM example_database.todo_list;
```
> We get the following result
![image](https://user-images.githubusercontent.com/64647166/162278590-f2b070bb-46fd-40eb-a480-a16fc5781101.png)

> Now create a PHP script that will connect to MySQL and query for your content by issueing the following command and code.
```Bash
nano /var/www/projectLEMP/todo_list.php
```
```PHP
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
> You can now access this page over the internet using the public IP or public domain name.

http://<Public_domain_or_IP>/todo_list.php

> You should get the following results

![image](https://user-images.githubusercontent.com/64647166/162281072-879da198-4d28-4002-89ee-d09dfd37b82f.png)
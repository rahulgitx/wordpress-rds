# Launching WordPress with AWS RDS

## Overview
Launching WordPress on an RHEL AWS instance and use AWS RDS with MySQL as its database. All the resources used will be of free tier.
## Launching database on AWS RDS
Basic steps on how to launch a database server on AWS RDS:
* Choose standard create.
* Select MySQL in engine options.
* Choose the latest version of MySQL.
* Give a name to your RDS instance (this name is associated with the resources of your AWS account and not with the upcoming configurations in WordPress).
* Choose burstable classes to get a t2.micro instance for the server.
* Give allocated storage (cannot be less than 20GiB).
* Choose a VPC ( go for default if you haven't created any by yourself).
* Choose a security group of create your own at that instance. Let the inbound rule be as it is just edit the IP address range that can access the server and put the IP of instance on which you’ll be launching WordPress.
* Go for authentication just by password to make it simple.
* In additional service you can enter the database name or you can create one by going into your server from any other ec2-instance. You’ll need this database created for WordPress. You can do this way to make it simple or go with how it is being dictated (the database will be created from inside a different server with mysql)
* Create database.

After your database has been created go inside your database and note the end-point provided to you. This is the host name that is used to access your database server from any other OS.

## Launching WordPress
  First you'll need to have an OS on whose webserver we’ll launch our WordPress application. So, use a free tier t2.micro instance launched with redhat 8 image. 
  
  * Connect to the instance and download httpd software using ```yum install httpd -y```
  * Download WordPress with ```curl https://wordpress.org/latest.tar.gz --output wordpress.tar.gz``` and un-tar using ```tar xvf wordpress.tar.gz```
  * Move the files to /var/www/html with ```cp -rv wordpress/ /var/www/html``` and give permission to and give the ownership of the web root apache to let WordPress make changes on its own : ```chown -R apache:apache /var/www/html/wordpress```
  * WordPress is written on PHP and needs PHP software installed along with the plugins. The initial plugins required to run WordPress are: ```yum install php-mysqlnd php-json -y```. Later you can install php-fpm for more functionalities.
  * SELinux is one more thing that can get in your way. So, either make it permissive by ```setenforce 0``` or ```chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R```
  
 ### Creating database in MySQL
* Using any instance download mysql on it using ```yum install mysql -y```.
* Get inside your database server using ```mysql -h <end-point> -u <user-name> -p``` w```hich will prompt for password and give the one you created in AWS.
* Now simply just create the database with ```>create database wordpress_db;``` this will give you an output something like ```Query OK, 1 row affected (0.01 sec)```. 

You're now done with the configuration part. Go to your browser and open http://localhost:<port> (port would be 3306 for default). 
 
You might see a page to select language. Select and continue to the page where you'll be asked to submit the connection details of the database.
  
Enter the details :
* Database name : the one you created inside the database or one named while making RDS.
* Username : One you gave as master username in beginning of RDS creation.
* Password : The one you gave in RDS creation in database authentication.
* Dataset Host : Your database’s end-point.
 
Leave the last option if you're not going for multiple WordPress installation.
  
After this it will proceed to install and will be asked for credential authentications for logging into WordPress (not any of the previous usernames and passwords).
  
And its done, you’re WordPress application has been configured!

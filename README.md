
## Steps to deploy a Laravel application on AWS / Ubuntu server
You want to deploy your Laravel application on AWS EC2 instance/Ubuntu server using NGINX server, here are steps to follow. 

### **Prerequisites**
* Gitlab/Github account
* AWS account

### **Step 1 : Create an EC2 instance**
* Go to [https://aws.amazon.com/console/](https://aws.amazon.com/console/) and log in to your AWS account.
* Once in your dashboard, search or click on menu EC2 instances. Then click on launch instance.
* Follow basics steps to create the EC2 instance.
* SSH Login to your instance

### **Step 2 : Install Nginx web server**
In your server terminal, execute the following commands
``` console
sudo su
apt update
```
Install Nginx
``` console
apt install nginx
```

Install PHP and composer
``` console
apt install php php8.3-fpm php8.3-mysql php8.3-bcmath php8.3-cli php8.3-xml php8.3-gd php8.3-opcache php8.3-mbstring php8.3-zip -y

apt install zip unzip

apt install nodejs npm

apt install composer
```

Install Git
``` console
apt install git
```

Check the version of Nginx and PHP
``` console
nginx -v
php -v
```

### **Step 3: Clone the app project on server from Git**
In your server SSH terminal, type following commands:  
``` console
cd /var/www/html

git clone git@gitlab.com:yourName/YourProject.git
```
To have access rights to Git repository, you need to add SSH keys on server and in your Gitlab/Github account.

* In your server SSH terminal, generate a key pair by running the following command

``` console
ssh-keygen -t rsa -b 4096 -C "your_email@example.com
```

* Locate the public key, find the public key file (usually ~/.ssh/id_rsa.pub or the filename you specified). Copy the public key by opening the file and copy the entire content.
``` console
cat ~/.ssh/id_rsa.pub
```
Copy the content (in your clipboard).

* Navigate to your GitLab/Github settings

Go to **`Gitlab -> Edit Profile -> SSH keys -> New SSH key`**

Paste the public key into the **`Key`** field, provide a descriptive title, and click "Add SSH key".

Now you can clone the project
``` console
cd /var/www/html

git clone git@gitlab.com:yourName/YourProject.git
```


### **Step 4: Set up required permission for project**

Set proper ownership and permission for the project directory.

In your server SSH terminal, do the following : 

``` console
chown -R www-data:www-data /var/www/html/stock-app/

chmod -R 775 /var/www/html/stock-app/
```

> [!IMPORTANT]  
> **`www-data`** represents the user and group used by Nginx to execute files of your project. So if this Nginx's user doesn't have the right permissions, your web application won't be executed.



### **Step 5: Configure Nginx for Laravel**

Create an Nginx config file **`stock-app.conf`** for your Laravel app in **`/etc/nginx/conf.d/`**

``` console
cd /etc/nginx/conf.d

nano stock-app.conf
```
Copy the content of the nginx config file in my Github repository, paste it and save the file.

Now you need to tell Nginx to point your web application instead of default page.
``` console
cd /etc/nginx/

nano nginx.conf
```
In that file, comment line **`include /etc/nginx/sites-enabled/*`**

> [!NOTE]  
> If you don't have that line in your nginx.conf file, then comment all lines of **`server{ listen 80 ...}`**.

Check the Nginx syntax by typing

``` console
nginx -t
```

Enable and restart Nginx server

``` console
systemctl enable nginx

systemctl restart nginx
```

Confirm that your Nginx server is listening at port 80

``` console
lsof -i :80
```
If nothing is displayed, it means that there is a misconfiguration of the Nginx server.

### **Step 6: Run Laravel commands**

Before the app is launched, run the following commands

``` console
cd /var/www/html/stock-app

composer install

nano .env
```
Fill in the **`.env`** file with correct information. Then execute the following command

``` console
php artisan key:generate

npm install

npm run build
```


### **Step 7: Access the web app**

Open a web browser and go to your server public IP address by using only HTTP not HTTPS (as the SSL is not yet configured on the server).


1. Update
```
sudo yum update
hostnamectl # Check centOS info
yum install wget # Install wget
```
2. Add apt
```
yum install epel-release
```
3. Install nginx
```
yum install nginx
systemctl start nginx
systemctl enable nginx # Startup
```
4. Install php7.2
```
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum install yum-utils
subscription-manager repos --enable=rhel-7-server-optional-rpms
yum-config-manager --enable remi-php72
yum update
yum search php72 | more
yum install php72 php72-php-fpm php72-php-gd php72-php-json php72-php-mbstring php72-php-mysqlnd php72-php-xml php72-php-xmlrpc php72-php-opcache

```
/etc/nginx/conf.d/default
```
server {
    listen       80;
    server_name  server_domain_name_or_IP;

    # note that these lines are originally from the "location /" block
    root   /usr/share/nginx/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
@NOTE: If php72 -v change to php -v 
```
scl enable php72 bash
```
5. Install MySQL
```
yum install mysql-server
systemctl start mysqld

mysql -u root -p
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE dbname;
USE dbname;
\. path_sql_file.sql #import
```


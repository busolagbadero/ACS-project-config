#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls fs-069368a4c55890b5f:/ /var/www/
sudo yum install -y httpd 
sudo systemctl start httpd
sudo systemctl enable httpd
sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms
sudo yum install epel-release
sudo yum update
sudo yum install https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo yum module install php:remi-7.4
sudo yum module reset php -y
sudo yum module enable php:remi-7.4 -y
sudo yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
mkdir /var/www/html/
cp -R /wordpress/* /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/b-db-mysql.cfjx7hbhlkdy.us-east-2.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/Badmin/g" wp-config.php 
sed -i "s/password_here/admin12345/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd










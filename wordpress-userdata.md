#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-057c19e25825aa166 fs-085f0ddb585a8cab0:/ /var/www/
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
sed -i "s/localhost/lamislick-database-1.cp8ukuu2ieln.us-east-1.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/admin/g" wp-config.php 
sed -i "s/password_here/lamislick/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd










#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-087db5a73c7b8725b fs-0cf945c5a63141f5b:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/IwunzeGE/DevopsToolingWebsite.git
mkdir /var/www/html
cp -R /DevopsToolingWebsite/html/*  /var/www/html/
cd /DevopsToolingWebsite
mysql -h zhikbee-rds.cg22cjxlsbrj.us-east-2.rds.amazonaws.com -u admin -p toolingdb < tooling-db.sql
cd /var/www/html/
touch healthstatus
sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('zhikbee-rds.cg22cjxlsbrj.us-east-2.rds.amazonaws.com', 'admin', 'password', 'toolingdb');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd








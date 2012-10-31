Creating an Ubuntu/PHP/NGinx/Wordpress Site on Amazon
=====================================================
Disclaimer: 
YMMV! Default passwords are used in here. 
Don't blame me if you forget to change them!
By reading this you agree not to sue me for anything!
Use at your own risk!

Author's Notes:
This repo created on Oct 31 in response to the 
"10 million hits on a cheapo amazon server" posting
I wanted something to more rapidly (cut and paste)
create this setup so I can restore a Wordpress
backup to it. 5 million hits was more than enough
for me so I didn't do the last part.

Process:
1. Build a Ubuntu 12.04 LTS 32-bit instance
   on Amazon and Associate an IP address to it.
   Open up the firewall ports on Amazon for
   SSH, FTP, and HTTP (of course)

2. Grab your Amazon key and SSH into the instance
   as ubuntu
   
3. sudo bash

4. Copy / Paste the below and confirm (y).

ufw allow ssh
ufw allow http
ufw allow ftp
ufw logging off
ufw enable

5. Copy paste the below and confirm (y).

cd /tmp/
wget http://nginx.org/keys/nginx_signing.key
apt-key add /tmp/nginx_signing.key
apt-get update
apt-get -y install php5-fpm php-pear php5-common php5-mysql php-apc mysql-server proftpd

6. When asked for the Mysql password, be sure to remember it!

7. Copy / paste the below

mkdir /tmp/install
cd /tmp/install
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/default.conf
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/drop
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/nginx.conf
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/php.ini
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/proftpd.conf
wget https://raw.github.com/mindragon/NginxUbuntuDefaults/master/www.conf
cp -f /tmp/install/php.ini /etc/php5/fpm/php.ini
cp -f /tmp/install/proftpd.conf /etc/proftpd/proftpd.conf
cp -f /tmp/install/www.conf /etc/php5/fpm/pool.d/www.conf
apt-get -y install nginx
cp -f /tmp/install/default.conf /etc/nginx/conf.d/default.conf
cp -f /tmp/install/drop /etc/nginx/conf.d/drop
cp -f /tmp/install/nginx.conf /etc/nginx/nginx.conf
mkdir -p /var/www/
chown www-data:www-data /var/www/
chmod 775 /var/www
service nginx restart
service php5-fpm restart
cd /
rm -rf /tmp/install

8. Login to the mysql console 
   mysql -uroot -p(THE PASSWORD FROM STEP 6)
   
   Copy / paste the below (changing "PASSWORDFROMSTEP6) to your password from step #6)
   
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"root" IDENTIFIED BY "PASSWORDFROMSTEP6";
FLUSH PRIVILEGES;
EXIT;

9. Copy / paste the below

cd /tmp
wget http://wordpress.org/latest.tar.gz
tar zxvf latest.tar.gz
cd wordpress
mv * /var/www/
chown -R www-data:www-data /var/www
cp /var/www/wp-config-sample.php /var/www/wp-config.php
chown www-data:www-data /var/www/wp-config.php
passwd ubuntu

Enter a password for the system. This should be different from the WordPress password!

10. vi /var/www/wp-config.php and set the db_user and db_password to what you set in step #6

From here, you can either:
	
	a) Navigate to the site and verify that it works so that you can restore WordPress from a backup
	b) Navigate to https://api.wordpress.org/secret-key/1.1/salt/ and put the salt information in wp-config.php and create a new Wordpress

	Note -- if you choose option B, add the W3 Total Cache plug in and activate it for improved performance. 
    Navigate to the Performance section, select PHP APC in the cache options and enable DB Cache and Object Cache. Save all and Deploy.
    
 


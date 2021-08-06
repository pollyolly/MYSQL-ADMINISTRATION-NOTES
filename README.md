# MYSQL-SYSTEM-ADMINISTRATION

### GRANT USER PRIVILEGE ON DATABASE
```
SELECT User, Host FROM mysql.user; //check current user@host/localhost/ip
GRANT ALL PRIVILEGES ON databasename.* TO 'johndoe'@'10.20.20.%'; //set user privillege
```
### SHOW USERS
```
SELECT User, Host FROM mysql.user;
```
### CREATE NEW USER
```
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
```
### DROP USER
```
DROP USER 'jeffrey'@'localhost';
```
### SHOW DATABASES
```
SHOW DATABASES;
```
### SETUP MYSQL ROOT/USER PASSWORD
```
//If not work -new
// Worked on MYSQL Ver 8.0.25-0ubuntu0.20.04.1
$ sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NEW_USER_PASSWORD';

//If not work -new
$sudo service mysql stop
$sudo mysqld_safe --skip-grant-tables&

UPDATE mysql.user SET authentication_string=null WHERE User='root';
mysql -u root
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'yourpasswd';

//If not work above-old
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';

//If not work above-old
UPDATE mysql.user SET authentication_string = PASSWORD('NEW_USER_PASSWORD')
WHERE User = 'root' AND Host = 'localhost';

//If not work above-old
$ mysqld --initialize-insecure
$ mysql -u root --skip-password
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';
```
### SHOW DATABASE SIZES IN MegaBytes
```
SELECT table_schema "DB Name",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB" 
FROM information_schema.tables 
GROUP BY table_schema; 
```
### IMPORT / EXPORT
```
mysqldump -uroot -p database > database.sql //export

mysql -root -p database < database.sql //import
```
### INSTALL AND UNINSTALL
```
Install
$sudo apt update
$sudo apt install mysql-server
$sudo mysql_secure_installation

Uninstall
$service mysql stop
$sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
$sudo rm -rf /etc/mysql /var/lib/mysql
$sudo apt autoremove  (remove unncessary packages)
$sudo apt autoclean   (remove apt cache)
```
### TROUBLESHOOTING
```
Access denied root@localtion
- You need to reset the root password
PHP MySqli error
- Make sure to install php7.2-mysql
- Enable PHP mysqli extension
```

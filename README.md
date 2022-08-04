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
### CREATE USER WITH REMOTE CONNECTION
```
CREATE USER 'johndoe'@'10.20.20.%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON databasename.* TO 'johndoe'@'10.20.20.%';
FLUSH PRIVILEGES;
```
### SETUP REMOTE CONNECTION MYSQL
```
CREATE USER 'user'@'10.20.14.%' IDENTIFIED BY 'samplePassword';
GRANT ALL ON <dbname>.* TO 'user'@'10.20.14.%';
GRANT RELOAD ON *.* TO 'user'@'10.20.14.%';
FLUSH PRIVILEGES;
FLUSH HOSTS;

VM IP Address: 10.20.14.% through VPN etc.

nano /etc/mysql/mysql.conf.d/mysqld.cnf

#bind-address   = 127.0.0.1
bind-address    = <vm private ip>

Test connection by specifying host IP.

mysql -h <vmIP> -uuser -p
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

mysql -uroot -p database < database.sql //import
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
### BIN LOGs
```
delete bin_log if occupying large space or storage.
/var/lib/mysql

```
### MySQL Repair DB
```
REPAIR TABLE mysql.db;
REPAIR TABLE mysql.user;
```
### TROUBLESHOOTING
```
- Root without a PASSWORD: auth_socket
  ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;

  SELECT User,host,plugin from mysql.user;
 User             | host      | plugin                |
+------------------+-----------+-----------------------+
| root             | localhost | auth_socket           |
+------------------+-----------+-----------------------+
- Root using a PASSWORD
  UPDATE mysql.user set plugin='mysql_native_password' where user = 'root';
+------------------+-----------+-----------------------+
| User             | host      | plugin                |
+------------------+-----------+-----------------------+
| root             | localhost | mysql_native_password |
+------------------+-----------+-----------------------+

- Access denied root@localtion
  - You need to reset the root password
- PHP MySqli error
  - Make sure to install php7.2-mysql
  - Enable PHP mysqli extension
```

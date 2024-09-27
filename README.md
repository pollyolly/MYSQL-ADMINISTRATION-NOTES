# MYSQL-ADMINISTRATION

### GRANT USER PRIVILEGE ON DATABASE
```sql
SELECT User, Host FROM mysql.user; //check current user@host/localhost/ip
GRANT ALL PRIVILEGES ON databasename.* TO 'johndoe'@'10.20.20.%'; //set user privillege
```
### SHOW USERS
```sql
SELECT User, Host FROM mysql.user;
```
### CREATE NEW USER
```sql
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
```
### CREATE USER WITH REMOTE CONNECTION
```sql
CREATE USER 'johndoe'@'10.20.20.%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON databasename.* TO 'johndoe'@'10.20.20.%';
FLUSH PRIVILEGES;
```
### SETUP REMOTE CONNECTION MYSQL
```sql
CREATE USER 'user'@'10.20.14.%' IDENTIFIED BY 'samplePassword';
GRANT ALL ON <dbname>.* TO 'user'@'10.20.14.%';
GRANT RELOAD ON *.* TO 'user'@'10.20.14.%';
FLUSH PRIVILEGES;
FLUSH HOSTS;

VM IP Address: 10.20.14.% through VPN etc.

nano /etc/mysql/mysql.conf.d/mysqld.cnf

#bind-address   = 127.0.0.1
# bind-address    = 162.220.160.183 # VM IP Address, use 0.0.0.0 if not working
bind-address = 0.0.0.0 #bind any address
```
Test connection by specifying host IP.
```
$mysql -h <vmIP> -uuser -p
```
```
Allow Port
```vim
$sudo ufw allow 3306/tcp
```
### DROP USER
```sql
DROP USER 'imuser'@'localhost';
```
### SHOW DATABASES
```sql
SHOW DATABASES;
```
### SETUP MYSQL ROOT/USER PASSWORD
```vim
// WORKED on MYSQL Ver 8.0.25-0ubuntu0.20.04.1
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
```sql
SELECT table_schema "DB Name",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB" 
FROM information_schema.tables 
GROUP BY table_schema; 
```
### IMPORT / EXPORT
```vim
$mysqldump -uroot -p database > database.sql //export

$mysql -uroot -p database < database.sql //import
```
### INSTALL AND UNINSTALL
Install
```vim
$sudo apt update
$sudo apt install mysql-server
$sudo mysql_secure_installation
```
Uninstall
```vim
$service mysql stop
$sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
$sudo rm -rf /etc/mysql /var/lib/mysql
$sudo apt autoremove  (remove unncessary packages)
$sudo apt autoclean   (remove apt cache)
```
### BIN LOGs
```vim
delete bin_log if occupying large space or storage.
/var/lib/mysql

```
### MySQL Repair DB
```sql
REPAIR TABLE mysql.db;
REPAIR TABLE mysql.user;
```
### TROUBLESHOOTING
Root without a PASSWORD: auth_socket
```vim
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
```
Your password does not satisfy the current policy requirements
```
mysql> UNINSTALL COMPONENT 'file://component_validate_password'; //Remove
```
Access denied root@localtion
```
You need to reset the root password
```
PHP MySqli error
```
  - Make sure to install php7.2-mysql
  - Enable PHP mysqli extension
```
For M1 Installation
```
https://medium.com/@johnmark_76235/setup-php-nginx-and-php-fpm-on-m1-macbook-aeaea14d2675
```
### REFERENCES
[MySQL Remote Connection with MySQL Workbench on a Virtual Private Server](https://medium.com/@johnmark_76235/mysql-remote-connection-with-mysql-workbench-on-a-virtual-private-server-2e18d8ff78e4)

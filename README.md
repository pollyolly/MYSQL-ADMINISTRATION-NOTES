# MYSQL-ADMINISTRATION

### GRANT USER PRIVILEGE ON DATABASE
```sql
SELECT User, Host FROM mysql.user; /* check current user@host/localhost/ip */
GRANT ALL PRIVILEGES ON databasename.* TO 'johndoe'@'10.20.20.%'; /* set user privillege */
```
### SHOW USERS
```sql
SELECT User, Host FROM mysql.user;
```
### CREATE NEW USER
```sql
CREATE USER 'newuser'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'yourpasswd';
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
### RESET MYSQL ROOT PASSWORD
```vim
$service mysql stop
$mysqld_safe --skip-grant-tables &
```
mysqld_safe Directory '/var/run/mysqld' for UNIX socket file don't exists.
```vim
$mkdir /var/run/mysqld
$chown mysql:mysql /var/run/mysqld
```
mysqld_safe A mysqld process already exists or unable to service mysql start
```vim
$ps aux | grep mysqld
#mysql 648303
$kill 648303 
```
```vim
$mysql
```
```vim
UPDATE mysql.user SET authentication_string=null WHERE User='root';
UPDATE mysql.user SET plugin='caching_sha2_password' WHERE User='root';
FLUSH PRIVILEGES;
exit;
```
```vim
$mysql -u root
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'yourpasswd';
```
```vim
$mysqladmin -uroot -p shutdown
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
```vim
$mysql
```
for Latest and secured authentication plugin use caching_sha2_password
```vim
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'yourpasswd';
```
or for MySQL old versions compatibility use mysql_native_password plugin for authentication
```vim
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
```vim
mysql> FLUSH PRIVILEGES;
mysql> exit;
$service mysql restart
```
Check available authentication plugins
```sql
SELECT PLUGIN_TYPE, PLUGIN_STATUS, PLUGIN_NAME FROM INFORMATION_SCHEMA.PLUGINS;
/* caching_sha2_password, mysql_native_password, auth_socket, sha256_password */
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
Delete old binlog if occupying large storage.
```vim
$rm -r /var/lib/mysql/binlog.000001
```
Binlogs are records of all changes in DB use to revert back.
```vim
/var/lib/mysql/binlog.000003
```
### MySQL Repair DB
```sql
REPAIR TABLE mysql.db;
REPAIR TABLE mysql.user;
```
### Validate MySQL Configs
```
$mysqld --validate-config
```
### MySQL Consuming Large Memory
```
$service mysql status
#398M memory used
```
$vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
#Reduce memory usage by adding 
[mysqld]
table_definition_cache = 200
table_open_cache = 215
```
### TROUBLESHOOTING
Root password using Unix User Credentials (auth_socket)
```vim
ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
/* auth_socket, caching_sha2_password, mysql_native_password, sha256_password */
SELECT User,host,plugin from mysql.user where User = 'root';
+------------------+-----------+-----------------------+
| User             | host      | plugin                |
+------------------+-----------+-----------------------+
| root             | localhost | auth_socket           |
+------------------+-----------+-----------------------+
```
Root password using old password authentication plugin (mysql_native_password) for backward compatibility
```vim
UPDATE mysql.user set plugin='mysql_native_password' where User = 'root';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
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

[M1 Installation PHP, NginX and MySQL](https://medium.com/@johnmark_76235/setup-php-nginx-and-php-fpm-on-m1-macbook-aeaea14d2675)

### REFERENCES
[MySQL Remote Connection with MySQL Workbench on a Virtual Private Server](https://medium.com/@johnmark_76235/mysql-remote-connection-with-mysql-workbench-on-a-virtual-private-server-2e18d8ff78e4)

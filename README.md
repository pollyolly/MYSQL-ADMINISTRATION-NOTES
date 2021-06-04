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
### SHOW DATABASES
```
SHOW DATABASES;
```
### SETUP MYSQL ROOT PASSWORD
```
//If no password
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';

//If above not work
UPDATE mysql.user SET authentication_string = PASSWORD('NEW_USER_PASSWORD')
WHERE User = 'root' AND Host = 'localhost';

//If above all not work
$ mysqld --initialize-insecure
$ mysql -u root --skip-password
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';
```

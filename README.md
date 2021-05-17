# MYSQL-SYSTEM-ADMINISTRATION

### GRANT USER PRIVILEGE ON DATABASE
```
SELECT User, Host FROM mysql.user;
GRANT ALL PRIVILEGES ON databasename.* TO 'johnmark'@'10.20.20.%';
```
###SHOW USERS
```
SELECT User, Host FROM mysql.user;
```
#Show database;
```
SHOW DATABASES;
```

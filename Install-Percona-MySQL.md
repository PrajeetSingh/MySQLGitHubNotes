## Install Percona MySQL 8 Community Edition
### Installation Steps
Step 1: Download Percona MySQL 8 repository package 
```sh
wget https://repo.percona.com/yum/percona-release-latest.noarch.rpm
```
Step 2: Install Percona MySQL repo locally
```sh
sudo yum localinstall percona-release-latest.noarch.rpm
```
Step 3: Enable repo for Percona MySQL 8
```sh
sudo percona-release setup -y ps80
```
Step 4: Install MySQL Server
```sh
sudo yum install percona-server-server
```
Step 5: Enable MySQL service to auto-start on reboot
```sh
sudo systemctl enable mysqld
```
Step 6: Start MySQL service
```sh
sudo systemctl start mysqld
sudo systemctl status mysqld
```
Step 8: Verify MySQL PID, port and files
```sh
pidof mysqld
netstat -ntlp | grep 3306
sudo lsof -u mysql
```
Step 9: Secure the MySQL connection
```sh
sudo mysql_secure_installation
```
Remember: initial temporary password of root is in /var/log/mysqld.log. You need to change this password after your first login.

Step 10: Login to the MySQL server.
```sh
mysql -u root -p
```
Command to change passord of root is : alter user root@localhost identified by 'your-passowrd';

Step 11: Create a test database
```sql
create database mydb;
use mydb;
show tables;
```

Step 12: Configure passwordless login
```sh
mysql_config_editor set --host=localhost --login-path=client --user=root --password
# You will be prompted for password
```
Step 13: Check whether passwordless login is setup
```sh
mysql_config_editor print -all
```

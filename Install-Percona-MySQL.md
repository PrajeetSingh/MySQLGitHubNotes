## Install Percona MySQL 8 Community Edition
# Installation Steps
Step 1: Download Percona MySQL 8 repository package 
wget https://repo.percona.com/yum/percona-release-latest.noarch.rpm

Step 2: Install Percona MySQL repo locally
sudo yum localinstall percona-release-latest.noarch.rpm

Step 3: Enable repo for Percona MySQL 8
sudo percona-release setup -y ps80

Step 4: Install MySQL Server
sudo yum install percona-server-server

Step 5: Enable MySQL service to auto-start on reboot
sudo systemctl enable mysqld

Step 6: Start MySQL service
sudo systemctl start mysqld

Step 7: Check status of MySQL service
sudo systemctl status mysqld

Step 8: Verify MySQL PID, port and files
pidof mysqld
netstat -ntlp | grep 3306
sudo lsof -u mysql

Step 9: Secure the MySQL connection
sudo mysql_secure_installation

# Remember: initial temporary password fo root is in /var/log/mysqld.log. You need to change this password after your first login.
Step 10: Login to the MySQL server.
mysql -u root -p

-- Command to change passord is : alter user root@localhost identified by 'your-passowrd';

Step 11: Create a test database
create database mydb;
use mydb;


Step 12: Configure passwordless login
mysql_config_editor set --host=localhost --login-path=client --user=root --password
-- You will be prompted for password

Step 13: Check whether passwordless login is setup
mysql_config_editor print -all
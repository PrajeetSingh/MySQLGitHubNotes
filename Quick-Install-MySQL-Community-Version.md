#### Below is the script I use to quickly install MySQL Community version on newly created AWS VM
<!--
It is downloading mysql community version from Oracle's MySQL site, then installing, and starting MySQL
3306 is default port of MySQL
When MySQL is installed, temporary password of "root" user is put in /var/log/mysqld.log file.
"mysql -u root -p" will help you login to MySQL and prompt for password, you can put temporary password, then change that after login using "atler user" command.
--> 
I simply copy paste entire part of script on newly created server and run these
```sh
wget https://dev.mysql.com/get/mysql84-community-release-el9-1.noarch.rpm;
sudo dnf install mysql84-community-release-el9-1.noarch.rpm -y;
sudo dnf install mysql-community-server -y;
sudo systemctl enable mysqld.service;
sudo systemctl status mysqld.service;
sudo systemctl start mysqld.service;
sudo systemctl status mysqld.service;
pidof mysqld;
netstat -ntlp | grep 3306;
sudo cat /var/log/mysqld.log | grep 'password';
mysql -u root -p
```

#### Change password of root user
```sql
alter user root@localhost identified by 'Root@321'; exit;
```

<!--
Below command will help in setting password less entry for mysql "root" user.
-->
#### Setup password less entry for root
```sh
mysql_config_editor set --host=localhost --login-path=client --user=root --password

# Verify passwordless configuration
mysql_config_editor print --all
```

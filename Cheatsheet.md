### MySQL Cheatsheet

#### Startup / Shutdown / Status. For mariadb, instead of mysqld, it can be either mariadb or mysql, depending on version.
'''sh
sudo systemctl start mysqld
sudo systemctl stop mysqld
sudo systemctl status mysqld
'''

### Login to mysql
'''sql
mysql -u root -p
-- or
mysql
'''

### List databases
'''sql
show databases;
'''

### Switch to a database use it
'''sql
use myfancydatabase;
'''

### Create user and grant all permissions to it
'''sql
create user pj identified by 'Pj@123456';
grant all on *.* to pj;
'''

### Show current user
'''sql
select user();
select current_user();
'''

### Show current database
'''sql
select database();
'''

### Check DB Version
'''sql
show version;
select version();
'''


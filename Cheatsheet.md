## MySQL Cheatsheet

#### Startup / Shutdown / Status. For mariadb, instead of mysqld, it can be either mariadb or mysql, depending on version.
```sh
sudo systemctl start mysqld
sudo systemctl stop mysqld
sudo systemctl status mysqld
```

### Login to mysql
```sql
mysql -u root -p
-- or
mysql
```

### List databases
```sql
show databases;
```

### Switch to a database use it
```sql
use myfancydatabase;
```

### Create user and grant all permissions on all databases to it. 
#### Remember, we need to "flush" privileges after running "grant" command :)
```sql
create user pj identified by 'xxxxxx' password expire never account unlock;
-- or
create uesr pj identified by 'xxxxxx' password expire;
grant all on *.* to pj;
flush privileges;
```

### Show current user
```sql
select user();
select current_user();
```

### Show current database
```sql
select database();
```

### Check DB Version
```sql
show version;
select version();
```

### Execute SQL file
####  Inside MySQL session
```sql
source myscript.sql
\. myscript.sql
```
#### From O/S prompt
```sh
mysql --host=localhost myfancydatabase < myscript.sql
```
#### Using PIPE symbol
```sh
cat myscript.sql | mysql --host=localhost myfancydatabase
```


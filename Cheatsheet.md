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

### Check value of a variable / parameter
```sql
show global variables like 'max_connections';
show global variables like '%buffer_pool%';
```

### Show help
```sql
\h 
-- or 
\?
```

### Show status
```sql
\s
-- or
status
```

### Edit
```sql
\e 
-- or 
edit
```

### Clear screen
```sql
\c 
-- or 
clear
```

### Reconnect to server
```sql
\r 
-- or 
connect
```

### Display query results vertically
```sql
\G
```

### Set output file
```sql
\T or tee
\t or untee
```

### Show warnings after every statement
```sql
\W or warnings
\w or nowarning
```

### Some Show commands
```sql
show databases | tables | views | users;
show tables like '%view%';
show tables from myfancydatabase;
show binary logs;
show binlog events;
show engines;
show create table|user|database;
show errors;
show warnings;
show events;
show triggers;
show processlist;
-- Remember: we can use "like" clause in "show" command.
```

### Check Processlist
```sql
describe information_schema.processlist;
select id, user, db, time from information_schema.processlist;
-- or
show processlist;
```

### Check existing MySQL users
```sql
select user, host from user;
```
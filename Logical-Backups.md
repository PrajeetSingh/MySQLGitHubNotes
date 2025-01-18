## Logical Backups

In MySQL, logical backups can be taken using two utilities i.e., mysqldump and mysqlpump. mysqlpump is hte newer version of mysqlpump, it has parallelism too.

These utilities generate .sql files, which can then be used to restore backups.

Using these utilities, we can backup tables, tables with where clause, skipping a particular table, single database, multiple databases or all databases.

### mysqldump

#### Syntax:
```sh
mysqldump [options] db_name [table_name] > backup_name.sql
mysqldump [options] db_name [table_name] -where="condition" > backup_name.sql
mysqldump [options] db_name [table_name] -ignore-table=db.table_name > backup_name.sql
mysqldump [options] -databases db1 db2 ... > backup_name.sql
mysqldump [options] -all-databases > backup_name.sql
```

#### Example:
```sh
wget https://downloads.mysql.com/docs/world-db.zip
# Backup table city
mysqldump world city > city.sql

# Backup data from table city where district is Manitoba
mysqldump world city --where="District='Manitoba'"> ManitobaCities.sql

# Ignore table world.country, where world is the name of database
mysqldump world --ignore-table=world.country > without_country_table.sql
grep -i create without_country_table.sql

# Backup databases world and goodearth
mysqldump --databases world goodearth > world_goodearth.sql

# Backup all databases
mysqldump --all-databases > all_dbs.sql
grep "CREATE DATABASE /" all_dbs.sql
```

### Restore database
```sh
# Restore table city in database "world". Notice, we are giving name of database "world", table will be created in this database.
mysql world < city.sql

# Restore database world. Notice, we are not using just "mysql" and the script.
mysql < world.sql
```

### mysqlpump

This utility has more features than mysqldump. 

* Parallel processing of databases to speed up the dump process
* Better control over which database objects like tables, stored procedures, user accounts, etc to dump
* Dumping of user accounts as account-management statements rather than as inserts into the mysql system database
* Compress output & Dump progress, which is not shown in mysqldump 
* By default, mysqlpump dumps all the databases 
* mysqlpump was deprecated in version 8.0.34 and remove in higher versions 

### Syntax of mysqlpump 
```sh 
# If you just run mysqlpump will all database, it dump system tablespaces too and at the time of restore, it fails because system databases are already there.
# So, adding "--add-drop-database" or "--add-drop-table" help in these scenarios.
mysqlpump [options] db_name [table_name] --add-drop-table > backup_name.sql

# Below statement, backups all users including roles but exclude all databases.
mysqlpump [options] --execlude-databases=% -users > usres.sql
mysqlpump [options] --databases db1 db2 ... --add-drop-database > backup_name.sql
mysqlpump [options] --all-databases > backup_name.sql
```

### Examples of mysqlpump 
```sh
# Take a backup of "city" table.
mysqlpump world city > city_pump.sql
or
mysqlpump world city --add-drop-table > city_pump.sql

mysqlpump --databases world --add-drop-database > pump_world.sql

mysql < pump_world.sql
```

### Taking backup of DB Users and Roles 
Not all versions have both mysqlpump. Taking backup with mysqlpump is easier but if it is not available (like in some MySQL community versions), then we need to backup "mysql" database.
"mysql" database contains user and role details too and as backkup of mysqldump are in sql format, we can use these to recreate users and roles.

```sql 
select user, host from mysql.user order by 1;
```
#### Backup users/roles using mysqlpump utility
```sh
# This command will ignore all databases and backup users. Along with create user/role commands, it'll add "drop user" commands before the create commands.
mysqlpump --exclude-databases=% --users --add-drop-user > users.sql
or
# This command will backup mysql database, which contains create user/role commands in sql format
mysqlpump -u [username] -p --exclude-databases=* --database=mysql --result-file=mysqldb_backup.sql
```
#### Backup users/roles using mysqldump utility
```sh
mysqldump -u root -p mysql > mysqldb_backup.sql
```

### Compression in mysqlpump 
* By default mysqlpump does not compress output.
* Available options for compression algorithm for mysqlpump are LZ4 and ZLIB using option "--compress-output=LZ4|ZLIB". If we don't have these, then we nee dto install these.
* Compressed backups on one side can save a lot of storage space but restore may take longer, as we need to un-compress the backups first before restore.

#### Example 
```sh 
lz4 --version

# Perform backup
mysqlpump --databases <database_name> --compress-output=lz4 > mydb_pump.sql.lz4 

# Restore backup
lz4 -d mydb_pump.sql.lz4 mydb_pump.sql 
mysql < mydb_pump.sql
```

### Compression in  mysqldump
We do not have option like "--compress-output" in mysqldump, so we need to explicitly compress and decompress the backup.

#### Example 
```sh 
# Perform backup
mysqldump <database_name> | gzip > mydb_dump.sql.gz 

# Restore backup
gunzip < mydb.sql.gz | mysql -u <username> -p <database_name>

```
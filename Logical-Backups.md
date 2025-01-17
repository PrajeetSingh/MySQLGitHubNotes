## Logical Backups

In MySQL, logical backups can be taken using two utilities i.e., mysqldump and mysqlpump. mysqlpump is hte newer version of mysqlpump, it has parallelism too.

These utilities generate .sql files, which can then be used to restore backups.

Using these utilities, we can backup tables, tables with where clause, skipping a particular table, single database, multiple databases or all databases.

### mysqldump

Syntax:
```sh
mysqldump [options] db_name [table_name] > backup_name.sql
mysqldump [options] db_name [table_name] -where="condition" > backup_name.sql
mysqldump [options] db_name [table_name] -ignore-table=db.table_name > backup_name.sql
mysqldump [options] -databases db1 db2 ... > backup_name.sql
mysqldump [options] -all-databases > backup_name.sql
```

Example:
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
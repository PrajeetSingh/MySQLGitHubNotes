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
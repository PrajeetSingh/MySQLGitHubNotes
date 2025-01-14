## Clean Shutdown of MySQL Instance

Ensure that value of parameter innodb_fast_shutdown is set to 0, otherwise set it to 0 before shutting MySQL instance down.
There is no "shutdown immediate" in MySQL.

```sql
show global variables like 'innodb_fast_shutdown';
set global innodb_fast_shutdown=0;
sudo systemctl stop mysqld.service
```
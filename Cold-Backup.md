## Perform MySQL Cold Backup

### Step 1: Check location of data directory and system directory
```sql
show global variables like 'datadir';
show global variables like 'innodb_data%';
```

### Step 2: Clean shutdown MySQL instance
```sql
set global innodb_fast_shutdown=0;
```
```sh
sudo systemctl stop mysqld.service
```

### Take Cold Backup
```sh
# Take backup of Data Directory, System Directory and Option Files. 
sudo mkdir /tmp/mysql_coldbackup
sudo cp -r /var/lib/mysql /tmp/mysql_coldbackup
sudo cp -r /etc/mysql.cnf /tmp/mysql_coldbackup
```

**REMEMBER:** We don't need to backup binlogs, doublewrite files, redo logs, etc, as we have cleanly shutdown the MySQL Instance.
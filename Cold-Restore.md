## Perform MySQL Cold Restore

**Step 1:** Stop current MySQL Instance
```sh
sudo systemctl stop mysqld
sudo systemctl status mysqld
```

**Step 2:** Delete directory Data Directory and System Tablespaces
```sh
sudo ls -lrth /var/lib/mysql/
sudo rm -rf /var/lib/mysql/
sudo ls -lrth /var/lib/mysql
```

**Step 3:** Copy files from backup location to Data Directory and System tablespace location
```sh
sudo ls -lrth /tmp/coldbackup
sudo cp -r /tmp/cold/backup/mysql/ /var/lib/mysql/
sudo ls -lrth /var/lib/mysql
```

**Step 4:** Copy Option/Configuratoin files to /etc/
```sh
sudo ls -lrth /tmp/coldbackup/my.cnf
sudo cp -r /tmp/coldbackup/my.cnf /etc/
```

**Step 5:** Change permissions of "mysql" directory to mysql:mysql
```sh
sudo chown -R mysql:mysql /var/lib/mysql
```

**Step 5:** Startup MySQL Instance
```sh
sudo systemctl start mysqld
sudo systemctl status mysqld
```

**Step 6:** Check MySQL Logs
```sh
sudo less /var/log/mysqld.log
```

**Step 7:** Login to MySQL Instance and verify Restore
```sql
show databases;
use <mydatabase>;
show tables;
```

**Remember:** Now bin logs, doublewrite files, redo logs, undo tablespaces, etc should be present as database is running.
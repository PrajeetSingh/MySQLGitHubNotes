## XTRABACKUP - Hot Backup Tool
### Installation Steps

#### Download Percona XTRABACKUP rpm and Install it
```sh
wget https://downloads.percona.com/downloads/Percona-XtraBackup-8.4/Percona-XtraBackup-8.4.0-2/binary/redhat/9/x86_64/percona-xtrabackup-84-8.4.0-2.1.el9.x86_64.rpm

sudo yum install percona-xtrabackup-84-8.4.0-2.1.el9.x86_64.rpm
```

#### Verify XTRABACKUP installation
```sh
which xtrabackup

xtrabackup --version

xtrabackup --help
man xtrabackup
```

### Take backup using XTRABACKUP

```sh
# Create backup directory
mkdir hotbackup

# Run XTRABACKUP
sudo xtrabackup -p --backup --target-dir=./hotbackup
or
sudo xtrabackup -p --backup --target-dir=/home/ec2-user/hotbackup
or
sudo xtrabackup --user=root -p --backup --target-dir=/home/ec2-user/hotbackup

# When the "xtrabackup --backup" is done, transactional changes are stored in Binary logs. 
# Once this backup finishes, we need to run "xtrabackup --prepare", it'll apply all changes from Binary logs to the backup taken to make it consistent.
sudo xtrabackup -p --prepare --target-dir=./hotbackup
```

#### Check backup taken, also the xtrabackup files
```sh
ls -lrth hotbackup/

ls -lrth hotbackup/xtrabackup*

# Here, I would recommend to go through xtrabackup_info file in backup directory.
```

### Prepare and Restore from Hot Backup
```sh
# Copy all files and directories to Data Directory or respective directories
# And remember, anything that is not in hotbackup directory, we don't need to worry about that, otherwise XTRABACKUP would have copied these there, with exception of creating '#innodb_redo' directory.
sudo -i
cd hotbackup
pidof mysqld
systemctl stop mysqld
pidof mysqld
rm -rf /var/lib/mysql
mkdir /var/lib/mysql
cp -r ibdata1 mysql.ibd undo_002 undo_001 binlog.index binlog.000005 ib_buffer_pool sys world mydb mysql performance_schema /var/lib/mysql/
cd /var/lib/mysql
mkdir '#innodb_redo'
chown -R mysql:mysql /var/lib/mysql/
systemctl start mysqld
pidof mysqld

# Check log file for any error
less /var/log/mysqld.log
ls -lrth /var/lib/mysql
```


# Configure both Logical and Physical backup of a MariaDB Replica server.

Why Backup from the Replica?

✅ Backing up from a replica avoids load on the master

✅ Safer for consistent reads

✅ Reduces impact on production writes

## Prerequisites (For Replica)

### 1. Create a dedicated backup user (on the replica)

```sql
create user 'backup_user'@'localhost' identified by 'MySecretPassword!';
grant reload, lock tables, process, replication client, show databases, event, trigger, select on *.* to 'backup_user'@'localhost';
flush privileges;
```

### 2. Install required tools

```sh
sudo apt update && sudo apt install mariadb-client mariadb-backup
```

## Logical Backup from Replica

> CAUTION: Use `--single-transaction` and `--master-data=2`

### ➤ Full Dump with Replication Coordinates

```sh
mysqldump -u backup_user -p --all-databases --single-transaction --master-data=2 --routines --events --triggers > /backup/mariadb-replica-logical-$(date +%F).sql
```

* --single-transaction: ensures consistency for InnoDB
* --master-data=2: adds the CHANGE MASTER TO command in SQL (commented), capturing binlog position

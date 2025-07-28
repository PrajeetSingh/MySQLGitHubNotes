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

> REMEMBER: Use `--single-transaction` and `--master-data=2`

* --single-transaction: ensures consistency for InnoDB
* --master-data=2: adds the CHANGE MASTER TO command in SQL (commented), capturing binlog position

### Full Dump with Replication Coordinates using `mysqldump`

```sh
mysqldump -u backup_user -p --all-databases --single-transaction --master-data=2 --routines --events --triggers > /backup/mariadb-replica-logical-$(date +%F).sql
```

### Per-Database Dump (Optional)

```sh
for db in $(mysql -u backup_user -p -e 'SHOW DATABASES;' | grep -Ev '(Database|information_schema|performance_schema|mysql|sys)'); do
    mysqldump -u backup_user -p \
      --databases "$db" \
      --single-transaction \
      --master-data=2 \
      --routines --events --triggers \
      > /backup/${db}_replica_$(date +%F).sql
done
```

## Physical Backup from Replica using `mariabackup`

`mariabackup` is **replica-safe**, it's designed to perform hot backups while replica is running.

### Step 1: Backup with Replication Info

```sh
sudo mariabackup --backup --user=backup_user --password=MySecretPassword --target-dir=/backup/physical-replica-$(date +%F)
```

This captures a **hot copy**, including binlog coordinates (`xtrabackup_slave_info`)

### Step 2: Prepare backup for restore

```sh
sudo mariabackup --prepare --target-dir=/backup/physical-replica-$(date +%F)
```

### Step 3: Compress bacukp (Optional)

```sh
tar -czvf /backup/physical-replica-$(date +%F).tar.gz /backup/physical-replica-$(date +%F)
```

## Validate Backup Integrity

### Logical

```sh
mysqlcheck -u backup_user -p --all-databases
```

### Physical

```sh
grep -i "binlog" /backup/physical-replica-*/xtrabackup_binlog_info
```


## Backup Scripts

### Logical Backup Script

```sh
#!/bin/bash
set -e
BACKUP_DIR="/backup/logical"
mkdir -p "$BACKUP_DIR"
mysqldump -u backup_user -pYourPassword \
  --all-databases --single-transaction --master-data=2 \
  --routines --events --triggers \
  > "$BACKUP_DIR/mariadb-replica-$(date +%F).sql"
find "$BACKUP_DIR" -type f -mtime +7 -delete
```

### Physical Backup Script

```sh
#!/bin/bash
set -e
BACKUP_DIR="/backup/physical/replica-$(date +%F)"
mkdir -p "$BACKUP_DIR"
mariabackup --backup --user=backup_user --password=YourPassword --target-dir="$BACKUP_DIR"
mariabackup --prepare --target-dir="$BACKUP_DIR"
tar -czvf /backup/physical-replica-$(date +%F).tar.gz -C "$BACKUP_DIR" .
find /backup/physical -type f -name "*.tar.gz" -mtime +7 -delete
```

### Add backup scripts to Crontab

```sh
crontab -e
0 1 * * * /path/to/logical_replica_backup.sh
0 2 * * * /path/to/physical_replica_backup.sh
```

### Upload to GCS Bucket (Optional)

```sh
gsutil cp /backup/*.tar.gz gs://your-gcs-bucket/
gsutil cp /backup/*.sql gs://your-gcs-bucket/
```

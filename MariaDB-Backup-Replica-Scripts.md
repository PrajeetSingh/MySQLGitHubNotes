# Ready-to-use backup scripts, including GCS upload integration and logging

Below are two complete backup scripts tailored for your MariaDB replica on a GCP VM, including:

* Logical backup with replication info
* Physical backup with replication state and compression
* GCS upload
* Rotation of old backups (7-day retention)
* Logging

Make sure to adjust:

* YourPassword
* your-gcs-bucket-name
* EMAIL (optional for error alerts)

## Folder Structure (Suggested)

```sh
/opt/db_backup/
├── backup_logical_replica.sh
├── backup_physical_replica.sh
├── backup.log
└── creds.env   ← contains sensitive values (optional)
```

## creds.env (Optional - safer than hardcoding)

```sh
MYSQL_PASS='YourPassword'
GCS_BUCKET='your-gcs-bucket-name'
EMAIL='your@email.com'
```

Source it in scripts:

```sh
source /opt/db_backup/creds.env
```

## backup_logical_replica.sh

```sh
#!/bin/bash
set -e
source /opt/db_backup/creds.env

NOW=$(date +%F)
BACKUP_DIR="/backup/logical"
LOG="/opt/db_backup/backup.log"
SQL_FILE="${BACKUP_DIR}/mariadb-replica-${NOW}.sql"

mkdir -p "$BACKUP_DIR"

echo "[$(date)] Starting logical backup..." >> "$LOG"

mysqldump -u backup_user -p"$MYSQL_PASS" \
  --all-databases --single-transaction --master-data=2 \
  --routines --events --triggers \
  > "$SQL_FILE"

if [ $? -eq 0 ]; then
  echo "[$(date)] Logical backup completed: $SQL_FILE" >> "$LOG"
else
  echo "[$(date)] Logical backup FAILED" >> "$LOG"
  # Optional: send email alert
fi

# Upload to GCS
gsutil cp "$SQL_FILE" "gs://${GCS_BUCKET}/logical/"
echo "[$(date)] Uploaded logical backup to GCS" >> "$LOG"

# Delete backups older than 7 days
find "$BACKUP_DIR" -type f -name "*.sql" -mtime +7 -exec rm {} \;
echo "[$(date)] Deleted old logical backups" >> "$LOG"
```

## backup_physical_replica.sh

```sh
#!/bin/bash
set -e
source /opt/db_backup/creds.env

NOW=$(date +%F)
BASE_DIR="/backup/physical"
TARGET_DIR="${BASE_DIR}/replica-${NOW}"
TAR_FILE="${BASE_DIR}/replica-${NOW}.tar.gz"
LOG="/opt/db_backup/backup.log"

mkdir -p "$TARGET_DIR"

echo "[$(date)] Starting physical backup..." >> "$LOG"

mariabackup --backup \
  --user=backup_user --password="$MYSQL_PASS" \
  --target-dir="$TARGET_DIR"

mariabackup --prepare \
  --target-dir="$TARGET_DIR"

tar -czvf "$TAR_FILE" -C "$TARGET_DIR" .

if [ $? -eq 0 ]; then
  echo "[$(date)] Physical backup completed and archived: $TAR_FILE" >> "$LOG"
else
  echo "[$(date)] Physical backup FAILED" >> "$LOG"
  # Optional: send email alert
fi

# Upload to GCS
gsutil cp "$TAR_FILE" "gs://${GCS_BUCKET}/physical/"
echo "[$(date)] Uploaded physical backup to GCS" >> "$LOG"

# Delete old backups
find "$BASE_DIR" -type f -name "*.tar.gz" -mtime +7 -exec rm {} \;
echo "[$(date)] Deleted old physical backups" >> "$LOG"
```

## Schedule via Crontab

```sh
crontab -e
0 1 * * * /opt/db_backup/backup_logical_replica.sh >> /opt/db_backup/cron.log 2>&1
0 2 * * * /opt/db_backup/backup_physical_replica.sh >> /opt/db_backup/cron.log 2>&1
```

## Final Checklist

* Create backup_user with privileges
* Install mariadb-client, mariadb-backup, gsutil
* Store password securely (creds.env)
* Configure backup and log folder permissions
* Create GCS bucket & enable gsutil auth
* Schedule via cron

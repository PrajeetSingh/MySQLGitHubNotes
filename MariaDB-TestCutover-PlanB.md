# Test Cutover Plan with backups of Replica before the handover.

Plan to use local backups to restore the replicas to their pre-test state is technically possible and can work, but it comes with a significant risk you must manage.

The primary risk is binary log retention on your production master (Server A). While your test is running, Server A will continue generating new binary logs. If your test takes too long, Server A might automatically purge the old binary logs that your restored replicas will need to begin replication. If this happens, you will be forced to rebuild the replicas from a new master backup anyway.

## Before You Begin: The Critical Prerequisite

Before starting, you must verify the binary log retention policy on your production master (Server A).

```sql
show variables like '%expire_logs%';
show variables like 'max_binlog_size';
```

If expire_logs_days is set to 7, you have 7 days of logs. If it is 0, logs are not purged automatically by time, but may still be purged by size (max_binlog_size).

Assess the Risk: Ensure your planned test duration, plus a buffer, is safely within this retention period. If your retention is only 1 day, a 12-hour test is very risky. If retention is 7 days, a 12-hour test is perfectly safe.

## Step-by-Step Guide for Plan

Here are the complete steps for the backup, test, and restore/resume method.

### Phase 1: Preparation and Backup (Pre-Test)

1. Stop Replication on Server B (Master Replica)

```sql
-- Run on Server B
stop slave;
show slave status\G;
```

Carefully record the `Master_Log_File` and `Exec_Master_Log_Pos`. You will need these exact values later.

2. Stop Replication on Server C (Cascading Replica)

```sql
-- Run on Server C
stop slave;
```

3. Take Backups: Use a tool like mariadb-backup for a fast, consistent physical backup.

```sh
# Run on Server B to back it up
mariadb-backup --backup --target-dir=/path/to/local/backups/server_b_backup --user=root --password
mariadb-backup --prepare --target-dir=/path/to/local/backups/server_b_backup

# Run on Server C to back it up
mariadb-backup --backup --target-dir=/path/to/local/backups/server_c_backup --user=root --password
mariadb-backup --prepare --target-dir=/path/to/local/backups/server_c_backup
```

### Phase 2: Perform the Test Cutover

1. Promote Server B

```sh
-- Run on Server B
set global read_only=0;
reset slave all;
```

2. Re-point Server C to Server B

Get the new coordinates from Server B (`show master status;`) and use them in the `CHANGE MASTER TO` command on Server C, then `START SLAVE`.

3. Handover for Testing

Provide the Server B to developers and testers.

### Phase 3: Revert and Resume (Post-Test)

This is where you restore from your local backups to revert the replicas to their original state.

1. Prepare the Backups

You must run the `--prepare` step on each backup (if it was not earlier).

```sh
# Run on the server holding the backups
mariadb-backup --prepare --target-dir=/path/to/local/backups/server_b_backup
mariadb-backup --prepare --target-dir=/path/to/local/backups/server_c_backup
```

2. Stop MariaDB and Wipe Data

On both Server B and Server C, stop the service and completely empty the data directory. This ensures a clean restore.

```sh
# Run on Server B and Server C
systemctl stop mariadb
rm -rf /var/lib/mysql/*
```

3. Restore the Data

```sh
# Run on Server B
sudo mariadb-backup --copy-back --target-dir=/path/to/local/backups/server_b_backup
sudo chown -R mysql:mysql /var/lib/mysql

# Run on Server C
sudo mariadb-backup --copy-back --target-dir=/path/to/local/backups/server_c_backup
sudo chown -R mysql:mysql /var/lib/mysql
```

4. Restart MariaDB and Re-establish Replication

Start the MariaDB service on both servers. Then, reconfigure replication using the exact coordinates you saved in Phase 1.

```sh
# Start the service on Server B and C
sudo systemctl start mariadb
```

```sql
# Configure Server B to follow Server A again
-- Run on Server B
change master to
  master_host='IP_OF_SERVER_A',
  master_user='your_replication_user',
  master_password='your_password',
  master_log_file='<Saved_Master_Log_File_from_Phase_1>',
  master_log_pos=<Saved_Exec_Master_Log_Pos_from_Phase_1>;
start slave;

# Configure Server C to follow Server B again
-- Run on Server C
-- (You will need to get Server B's new master coordinates after it has started replicating)
-- ... then run CHANGE MASTER TO and START SLAVE
```

5. Monitor the Lag

On Server B, run `show slave status\G` periodically. The Seconds_Behind_Master will be high initially but should steadily decrease as it processes the logs from the production master that were generated during your test. Once it reaches 0, your replica is fully in sync and the process is complete.

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

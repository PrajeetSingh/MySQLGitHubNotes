## Percona Toolkit - pt-mysql-summary example

```sh
~# pt-mysql-summary --user=root --password=password
mysql: [Warning] Using a password on the command line interface can be insecure.
# Percona Toolkit MySQL Summary Report #######################
              System time | 2025-02-16 16:40:11 UTC (local TZ: UTC +0000)
# Instances ##################################################
  Port  Data Directory             Nice OOM Socket
  ===== ========================== ==== === ======
# MySQL Executable ###########################################
       Path to executable | /usr/sbin/mysqld
              Has symbols |
# Slave Hosts ################################################
No slaves found
# Report On Port 3306 ########################################
                     User | root@localhost
                     Time | 2025-02-16 16:40:11 (UTC)
                 Hostname | ip-<your-ip-address-here>
                  Version | 8.0.30-22 Percona Server (GPL), Release '22', Revision '7e301439b65'
                 Built On | Linux x86_64
                  Started | 2025-02-16 15:33 (up 0+01:06:27)
                Databases | 5
                  Datadir | /var/lib/mysql/
                Processes | 1 connected, 2 running
              Replication | Is not a slave, has 0 slaves connected
                  Pidfile | /var/run/mysqld/mysqld.pid (exists)
# Processlist ################################################

  Command                        COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  Daemon                                1       1      4000      4000
  Query                                 1       1         0         0

  User                           COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  event_scheduler                       1       1      4000      4000
  root                                  1       1         0         0

  Host                           COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  localhost                             2       2      4000      4000

  db                             COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  NULL                                  2       2      4000      4000

  State                          COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  Waiting on empty queue                1       1      4000      4000
  init                                  1       1         0         0

# Status Counters (Wait 10 Seconds) ##########################
Variable                                Per day  Per second     10 secs
Aborted_connects                            100
Binlog_snapshot_position                  12500
Binlog_cache_use                             20
Binlog_stmt_cache_use                        20
Bytes_received                            50000                     500
Bytes_sent                              1000000          10        3500
Com_change_db                                20
Com_create_db                                20
Com_create_table                            800
Com_flush                                    45
Com_grant                                    20
Com_select                                   90                       2
Com_set_option                               90
Com_show_grants                              45
Com_show_status                              45
Com_show_variables                           20
Connections                                 350                       1
Created_tmp_files                           100
Created_tmp_tables                           70
Error_log_buffered_bytes                 300000           3
Error_log_buffered_events                  1750
Error_log_latest_write              40000000000000000 450000000000
Flush_commands                               70
Handler_commit                            12500
Handler_delete                              175
Handler_external_lock                    150000           1           9
Handler_prepare                              90
Handler_read_first                         1000
Handler_read_key                          40000                       3
Handler_read_next                         90000           1           7
Handler_read_rnd_next                     60000                     125
Handler_update                             7000
Handler_write                             25000                      60
Innodb_buffer_pool_bytes_data         500000000        6000        3500
Innodb_buffer_pool_pages_flushed           4500
Innodb_buffer_pool_pages_old              12500
Innodb_buffer_pool_read_requests         350000           4          20
Innodb_buffer_pool_reads                  30000
Innodb_buffer_pool_write_requests         40000                       4
Innodb_data_fsyncs                         2500
Innodb_data_read                      500000000        6000        3500
Innodb_data_reads                         30000
Innodb_data_writes                         6000
Innodb_data_written                    70000000         900        1750
Innodb_dblwr_pages_written                 1250
Innodb_dblwr_writes                         400
Innodb_redo_log_uuid                22500000000      250000
Innodb_redo_log_checkpoint_lsn        800000000        9000
Innodb_redo_log_current_lsn           800000000        9000
Innodb_redo_log_flushed_to_disk_lsn   800000000        9000
Innodb_redo_log_logical_size              10000
Innodb_redo_log_physical_size          70000000         800
Innodb_redo_log_capacity_resized     2250000000       25000
Innodb_log_write_requests                 17500
Innodb_log_writes                           800
Innodb_lsn_current                    800000000        9000
Innodb_lsn_flushed                    800000000        9000
Innodb_lsn_last_checkpoint            800000000        9000
Innodb_master_thread_active_loops            70
Innodb_master_thread_idle_loops           90000                       1
Innodb_max_trx_id                        175000           2
Innodb_os_log_fsyncs                        700
Innodb_os_log_written                   1500000          20
Innodb_pages_created                       3000
Innodb_pages_read                         30000
Innodb_pages0_read                          200
Innodb_pages_written                       4500
Innodb_purge_trx_id                      175000           2
Innodb_system_rows_deleted                  175
Innodb_system_rows_inserted                 200
Innodb_system_rows_read                  100000           1          10
Innodb_system_rows_updated                 7000
Innodb_num_open_files                       350
Innodb_undo_tablespaces_total                45
Innodb_undo_tablespaces_implicit             45
Innodb_undo_tablespaces_active               45
Innodb_secondary_index_triggered_cluster_reads       45000                       3
Mysqlx_port                              700000           8
Mysqlx_ssl_ctx_verify_mode                  100
Mysqlx_worker_threads                        45
Net_buffer_length                        350000           4
Open_table_definitions                     1000
Opened_files                                 45
Opened_table_definitions                   1750
Opened_tables                              3500                       2
Performance_schema_session_connect_attrs_longest_seen        2500
Queries                                     350                       5
Questions                                   350                       5
Select_scan                                 125
Ssl_session_cache_timeout                  7000
Table_locks_immediate                        70
Table_open_cache_hits                     70000                       2
Table_open_cache_misses                    3500                       2
Threads_created                              20
Uptime                                    90000           1           1
# Table cache ################################################
                     Size | 4000
                    Usage | 2%
# Key Percona Server features ################################
      Table & Index Stats | Disabled
     Multiple I/O Threads | Enabled
     Corruption Resilient | Enabled
      Durable Replication | Not Supported
     Import InnoDB Tables | Not Supported
     Fast Server Restarts | Not Supported
         Enhanced Logging | Disabled
     Replica Perf Logging | Disabled
      Response Time Hist. | Not Supported
          Smooth Flushing | Not Supported
      HandlerSocket NoSQL | Not Supported
           Fast Hash UDFs | Unknown
# Percona XtraDB Cluster #####################################
mysql: [Warning] Using a password on the command line interface can be insecure.
# Plugins ####################################################
       InnoDB compression | ACTIVE
# Schema #####################################################
Specify --databases or --all-databases to dump and summarize schemas
# Noteworthy Technologies ####################################
                      SSL | No
     Explicit LOCK TABLES | No
           Delayed Insert | No
          XA Transactions | No
              NDB Cluster | No
      Prepared Statements | No
 Prepared statement count | 0
# InnoDB #####################################################
                  Version | 8.0.30-22
         Buffer Pool Size | 128.0M
         Buffer Pool Fill | 20%
        Buffer Pool Dirty | 0%
           File Per Table | ON
                Page Size | 16k
            Log File Size | 2 * 48.0M = 96.0M
          Log Buffer Size | 16M
             Flush Method | fsync
      Flush Log At Commit | 1
               XA Support |
                Checksums |
              Doublewrite | ON
          R/W I/O Threads | 4 4
             I/O Capacity | 200
       Thread Concurrency | 0
      Concurrency Tickets | 5000
       Commit Concurrency | 0
      Txn Isolation Level |
        Adaptive Flushing | ON
      Adaptive Checkpoint |
           Checkpoint Age | 0
             InnoDB Queue | 0 queries inside InnoDB, 0 queries in queue
       Oldest Transaction | 0 Seconds
         History List Len | 0
               Read Views | 0
         Undo Log Entries | 0 transactions, 0 total undo, 0 max undo
        Pending I/O Reads | 0 buf pool reads, 0 normal AIO, 0 ibuf AIO, 0 preads
       Pending I/O Writes | 0 buf pool (0 LRU, 0 flush list, 0 page); 0 AIO, 0 sync, 0 log IO (0 log, 0 chkp); 0 pwrites
      Pending I/O Flushes | 0 buf pool, 0 log
       Transaction States | 3xnot started
# MyISAM #####################################################
                Key Cache | 8.0M
                 Pct Used | 20%
                Unflushed | 0%
# Security ###################################################
                    Users | 1 users, 0 anon, 0 w/o pw, 0 old pw
            Old Passwords |
# Encryption #################################################
mysql: [Warning] Using a password on the command line interface can be insecure.
No keyring plugins found
# Binary Logging #############################################
                  Binlogs | 2
               Zero-Sized | 0
               Total Size | 747.0
            binlog_format | ROW
         expire_logs_days | 0
              sync_binlog | 1
                server_id | 1
             binlog_do_db |
         binlog_ignore_db |
# Noteworthy Variables #######################################
     Auto-Inc Incr/Offset | 1/1
   default_storage_engine | InnoDB
               flush_time | 0
             init_connect |
                init_file |
                 sql_mode | STRICT_ALL_TABLES,NO_ENGINE_SUBSTITUTION
         join_buffer_size | 256k
         sort_buffer_size | 256k
         read_buffer_size | 128k
     read_rnd_buffer_size | 256k
       bulk_insert_buffer | 0.00
      max_heap_table_size | 16M
           tmp_table_size | 16M
       max_allowed_packet | 64M
             thread_stack | 1M
                      log |
                log_error | /var/log/mysql/error.log
             log_warnings |
         log_slow_queries |
log_queries_not_using_indexes | OFF
        log_slave_updates | ON
# Configuration File #########################################
              Config File | /etc/mysql/my.cnf
# Memory management library ##################################
jemalloc enabled in mysql config for process with id 1288
Using jemalloc from /usr/lib/x86_64-linux-gnu/libjemalloc.so.2
# The End ####################################################
```
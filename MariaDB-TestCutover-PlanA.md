# Test Cutover Plan

Steps to perform a test cutover by promoting your replica for developers and testers, while ensuring the production master remains completely unaffected.

This plan assumes the following server roles:

> Server A: The current Production Master.
> Server B: The Primary Replica (your test target).
> Server C: The Cascading Replica (replicates from Server B).

--------------------------------------------------------------------------------------------

## Phase 1: Preparation (Before the Test)

Before you begin, ensure your replication is healthy and you have a clear starting point.

1. Verify Replication Health: On Server B, check that replication from Server A is running perfectly.

```sql
show slave status\G;
```

Confirm that Slave_IO_Running and Slave_SQL_Running are both Yes, and Seconds_Behind_Master is 0.

2. Record Final Replication Position: On Server B, record the last coordinates from Server A. This is your "point-in-time" snapshot before the test.

```sql
stop slave;
show slave status\G;
```

Note down the values for Master_Log_File and Exec_Master_Log_Pos. These are the last transactions received and executed from the production master.

## Phase 2: The Test Cutover Execution

These steps will isolate Server B and promote it to a standalone master for the test environment.

1. Promote Server B (The New Test Master)

Now that replication is stopped, make Server B writable and sever its connection to the production master (Server A). This formally makes it an independent server.

```sql
-- Run these commands on Server B
set global read_only=0;
reset slave all;
```

* `set global read_only=0;` allows developers and testers to write to the database.
* `reset slave all;` completely erases the replication configuration from Server A, preventing any accidental reconnection.

2. Re-point the Cascading Replica (Server C)

Server C was replicating from Server B. Now that Server B is an independent master, you need to point Server C to its new binary log.

On Server B (New Test Master): Get its own master coordinates.

```sql
show master status;
```

Note the new File and Position values.

On Server C (Cascading Replica): Stop replication and point it to Server B's new coordinates.

```sql
-- Run these commands on Server C
stop slave;
change master to
  master_host='IP_OF_SERVER_B',
  master_user='your_replication_user',
  master_password='your_password',
  master_log_file='<File from Server B>',
  master_log_pos=<Position from Server B>;
start slave;
show slave status\G;
```

Verify that replication from Server B to Server C is now working.

3. Handover for Testing

The test cutover is complete. Server B is now the primary database for your test environment. Provide its IP address or hostname to the developers and testers. Production traffic on Server A has not been affected.

## Phase 3: Post-Test Plan (Crucial)

Once testing is finished, Server B will have different data than your production master (Server A). You cannot simply resume replication.

The safest and recommended action is to rebuild Server B.

Decommission the Test Environment: Shut down applications connected to Server B.

Rebuild Server B: Take a new backup of your production master (Server A) and restore it onto Server B.

Re-establish Replication: Configure Server B as a replica of Server A again, just as it was before the test.

Rebuild Server C: If necessary, repeat the process for the cascading replica, pointing it to the newly rebuilt Server B.

This ensures you have a clean, consistent replica ready for your actual production cutover.

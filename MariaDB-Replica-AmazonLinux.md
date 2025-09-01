# Below steps are to create a Test Master and Replica DBs on Amazon Linux in AWS

1. Initial Server Setup

First, rename the hostnames on each server to easily identify them. You'll need to log out and log back in to see the change in your terminal prompt.

hostnamectl set-hostname mariadb-master

hostnamectl set-hostname mariadb-replica

Relogin

Configure your AWS Security Group. 
Ensure the master server allows inbound traffic on TCP port 3306 from the private IP address of your replica server. This is crucial for them to communicate.

2. Install MariaDB on Both Servers
Run these commands on both the master and replica servers.

dnf update -y
dnf install mariadb105-server -y

systemctl start mariadb
systemctl enable mariadb

mysql_secure_installation

3. Configure the Master Server 

These steps are only for the master server (mariadb-master).

vi /etc/my.cnf.d/mariadb-server.cnf
[mysqld]
server_id=1
log-bin=/var/log/mariadb/binlog/mysql-bin
relay_log=/var/log/mariadb/relaylog/replica-bin
relay_log_index=/var/log/mariadb/relaylog/replica-bin.index
log_slave_updates=1
binlog_format=ROW

mkdir -p /var/log/mariadb/binlog
chown -R mysql:mysql /var/log/mariadb/
vi /etc/my.cnf.d/mariadb-server.cnf

systemctl restart mariadb

mysql -u root -p

create user 'replica_user'@'REPLICA_PRIVATE_IP' identified by 'your_strong_password';
grant replication slave on *.* to 'replica_user'@'REPLICA_PRIVATE_IP';
flush privileges;

172.31.45.149
create user 'replica_user'@'172.31.45.149' identified by 'my135pass790';
grant replication slave on *.* to 'replica_user'@'172.31.45.149';

show master status;
exit;

4. Configure the Replica Server
These steps are only for the replica server (mariadb-replica).

vi /etc/my.cnf.d/mariadb-server.cnf
[mysqld]
server_id=2
log-bin=/var/log/mariadb/binlog/mysql-bin
relay_log=/var/log/mariadb/relaylog/replica-bin
relay_log_index=/var/log/mariadb/relaylog/replica-bin.index
log_slave_updates=1
binlog_format=ROW
read_only = 1


mkdir -p /var/log/mariadb/relaylog
mkdir -p /var/log/mariadb/binlog
chown -R mysql:mysql /var/log/mariadb/
vi /etc/my.cnf.d/mariadb-server.cnf

systemctl restart mariadb

mysql -u root -p

stop slave;

change master to
  master_host='MASTER_PRIVATE_IP',
  master_user='replica_user',
  master_password='your_strong_password',
  master_log_file='mysql-bin.00000X',
  master_log_pos=XXX;
  
start slave; 

show slave status\G

mysql -u root -p -e "create database test_replication;"

mysql -u root -p -e "show databases;"



To demote a master to a replica:
SET GLOBAL read_only = 1;

To promote a replica to a master:
SET GLOBAL read_only = 0;
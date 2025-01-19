## MySQL Hot Backup
It is a Physical hot backup. Physically copy database files to a backup device while MySQL is online.

### Hot Backup Tools 
* Oracle provides mysqlbackup.
* MariaDB provides mariabackup.
* Percona provides xtrabackup.

### mysqlbackup
This is an Enterprise grade backup tool. It is not free and is available with Enterprise Edition of MySQL only.

### mariabackup
* It is an open-source, free tool provided by MariaDB.
* It is a Forked copy of Xtrabackup
* It supports all the main features of Percona Xtrabackup tool

### Xtrabackup
* It is an open-source, production grade (enterprise ready) hot backup tool for MySQL, from Percona. It is free of cost, no license is required.
* It does not create any locks on database during backup, backup runs seamless, without disrupting the performance of MySQL database
* This tool does not get installed along with Percona MySQL, we need to install it separately by downloading from https://www.percona.com/downloads 
* Compatible with on-prem as well as in the Cloud
* Point-in-time recoveries are possible with this.



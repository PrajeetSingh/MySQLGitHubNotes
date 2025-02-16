# Percona Toolkit
It is a collection of isolated, handy small programs that are bundled together, hence called a Toolkit.

It is free and is fully compatible with MariaDB, MongoDB, Oracle MySQL and Percona MySQL.

If we are using Percona MySQL and we want to open a ticket with Percona, then first thing they would like us to do is run Percona Toolkit Utilities to provide them the vital information about systems.

## Features of Percona Toolkit
* Open-Source and 100% free
* Fully compatible with MariaDB, MongoDB, Oracle MySQL and Percona MySQL
* Finds replica hosts
* Finds duplicate indexes
* Summarize MySQL server information
* Summarize MySQL host information
* Collect vital system information when problem occurs

### Installation Steps

**Step1:** Download Percona Repository file
```sh
sudo apt update && sudo apt -y install gnupg2 wget
wget https://repo.percona.com/apt/percona-release_latest.$(lsb_release -sc)_all.deb
```
**Step2:** Install Percona repository package
```sh
sudo dpkg -i percona-release_latest.$(lsb_release -sc)_all.deb
```
**Step3:** Enable Percona repository
```sh
sudo percona-release setup -y ps80
```
**Step4:** Install Percona Toolkit
```sh
sudo apt update
sudo apt install percona-toolkit
```
**Step5:** Verify the installation
```
dpkg -l | grep percona-toolkit
ls /usr/bin/pt-*

~# ls /usr/bin/pt-*
/usr/bin/pt-align            /usr/bin/pt-duplicate-key-checker  /usr/bin/pt-heartbeat    /usr/bin/pt-mysql-summary         /usr/bin/pt-sift           /usr/bin/pt-summary         /usr/bin/pt-variable-advisor
/usr/bin/pt-archiver         /usr/bin/pt-fifo-split             /usr/bin/pt-index-usage  /usr/bin/pt-online-schema-change  /usr/bin/pt-slave-delay    /usr/bin/pt-table-checksum  /usr/bin/pt-visual-explain
/usr/bin/pt-config-diff      /usr/bin/pt-find                   /usr/bin/pt-ioprofile    /usr/bin/pt-pmp                   /usr/bin/pt-slave-find     /usr/bin/pt-table-sync
/usr/bin/pt-deadlock-logger  /usr/bin/pt-fingerprint            /usr/bin/pt-kill         /usr/bin/pt-query-digest          /usr/bin/pt-slave-restart  /usr/bin/pt-table-usage
/usr/bin/pt-diskstats        /usr/bin/pt-fk-error-logger        /usr/bin/pt-mext         /usr/bin/pt-show-grants           /usr/bin/pt-stalk          /usr/bin/pt-upgrade
```

### pg-mysql-summary -- Getting MySQL Report
```sh
# Before running, pls ensure that root@localhost has privileges to run it.
show grants for 'root'@'localhost';
grant all privileges on *.* to 'root'@'localhost' with grant option;
flush privileges;

# Now execute pt-mysql-summary
pt-mysql-summary --user=root --password=<yourpassword>
```
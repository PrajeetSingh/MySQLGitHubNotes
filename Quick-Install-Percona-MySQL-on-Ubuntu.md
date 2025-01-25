## Quick Install script of Percona MySQL on Ubuntu

I use this script to quickly install Percona MySQL on EC2 Ubuntu instance to test things.

Only installation required is of "percona-server-server". Rest are optional and as per your requirement. I have put ; at the end to run them together.

**CAUTION:** If we try to install and use mysql_config_editor, that will remove percona-server-server, percona-server-client and percona-server-rocksdb, as mysql_config_editor is an Oracle community version utility.

Tip: When we reinstall percona-server-server, percona-server-client and percona-server-rocksdb again after installation of mysql_config_editor, myysql_config_editor and percona mysql, both start working fine.

```sh
sudo apt update;
sudo apt install curl;
sudo apt install unzip;
curl -O https://repo.percona.com/apt/percona-release_latest.generic_all.deb;
sudo apt install -y gnupg2 lsb-release ./percona-release_latest.generic_all.deb;
sudo apt update;
sudo percona-release setup ps80;
sudo percona-release enable ps-80 release;
sudo apt search percona;
sudo apt update;
sudo apt install -y percona-server-server;
sudo apt install -y percona-server-client;
sudo apt install -y percona-server-common;
sudo apt install -y percona-server-rocksdb;
sudo apt install -y percona-toolkit;
sudo apt install -y percona-xtrabackup;
sudo systemctl status mysql;
sudo systemctl enable mysql;
pidof mysql;
```
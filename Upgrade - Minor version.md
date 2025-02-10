## Upgrade minor version of Percona MySQL database

These are quick installation and upgrade steps for Minor version upgrade.

Minor version is when we remain on Major version, ex: 8.0 but we want to upgrade Minor version from 8.0.37 to 8.0.40. This could be for any bug fix or new feature.
Unlike Major version upgrade, Minor version upgrade is just updating Binaries of MySQL with newer version.

If you have replicas, then it is a good practice to perform Minor upgrade on Replica first then do it on Primary. It works fine if Primary and Replica are on different minor versions.

### INSTALL OLDER VERSION

```sh
# Check existing installations
dpkg -l | grep percona;
sudo apt list --installed | grep -i mysql-server;
sudo apt list | grep -i mysql-server;
mysql --version;

# Run o/s udpates
sudo apt update;
sudo apt upgrade;

# Configure Percona Repository
curl -O https://repo.percona.com/apt/percona-release_latest.generic_all.deb;
sudo apt install -y gnupg2 lsb-release ./percona-release_latest.generic_all.deb;
sudo apt update;
sudo percona-release setup ps80;
sudo percona-release enable ps-80 release;
sudo apt update;

# List available Percona versions 
apt list -a percona-server-server;

# Install any lower version
sudo apt-get install percona-server-client=8.0.37-29-1.noble percona-server-common=8.0.37-29-1.noble percona-server-server=8.0.37-29-1.noble;
sudo systemctl status mysql;
mysql -u root -p
```

```sql
select version();
show databases;
```

### NOW UPGRADE TO HIGHER MINOR VERSION

```sh
# Shutdown MySQL instance before upgrade 
sudo systemctl stop mysql;

# List available Percona versions 
apt list -a percona-server-server;

# Install higher version
sudo apt-get install percona-server-client=8.0.40-31-1.noble percona-server-common=8.0.40-31-1.noble percona-server-server=8.0.40-31-1.noble;
sudo systemctl status mysql;
mysql -u root -p
```

```sql
# Verify version
select version();
show databases;
```
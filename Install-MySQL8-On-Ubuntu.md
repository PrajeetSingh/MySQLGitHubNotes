## Install MySQL 8 community edition on Ubuntu

Step 1. Verify O/S version
```sh
lsb_release -a
```

Step 2. Run o/s updates and upgrade it to latest version
```sh
sudo apt update
sudo apt upgrade
```

Step 3. Install MySQL
```sh
sudo apt list --installed | grep -i mysql-server
sudo apt list | grep -i mysql-server
sudo apt install mysql-server-8.0
```

Step 4. Enable mysqld service to auto start after reboot
```sh
sudo systemctl enable mysql.service
```

Step 5. Start mysql service
```sh
sudo systemctl start mysql.service
sudo systemctl status mysql.service
```

Verify MySQL server's pid and the files in use
```sh
pidof mysqld
sudo lsof -u mysql
```
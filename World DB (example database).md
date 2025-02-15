## World Database (example database to test some data)

This is a sample / example database to get some data in MySQL database to play with.

You can use this database whenever you need some data or database to test something.

### Downlaod database file of World DB
```sh
wget https://downloads.mysql.com/docs/world-db.zip
```


### Unzip and load data in MySQL
```sh
sudo apt-get install unzip
unzip world-db.zip
cd world-db
mysql -u root -p < world.sql
```

### Verify database 
```sql
use world;
show tables;
select * from city limit 5;
```
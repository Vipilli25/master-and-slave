# master-and-slave

# Replication in Mysql
Configure MySql in master slave mode using student database.

## Installation
Used 2 ubuntu os as we need 2 servers running from different ip addresses.
One of the server will be the master and other server will be the slave.

### CONFIGURE MASTER 
Step 1: First we need to open the mysql configuration file using:
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
In the configuration file replace
* bind address with 0.0.0.0
* Uncomment server-id and give unique id.
* Uncomment log_bin directive to enable binary logging 
* Uncomment binlog_do_db directive and mention the database name which you want to replicate.

Step 2: Run mysql server now using:
```bash
sudo service mysql start
```
Step 3: Open mysql terminal for root user using:
```bash
sudo mysql -u root -p
```
Step 4: Create database and a table and Insert rows into the table using :

```bash
CREATE DATABASE students;
USE students;
CREATE TABLE stud (name VARCHAR(20),branch VARCHAR(25));
INSERT INTO stud VALUES ('Ricky', 'EEE');
INSERT INTO stud VALUES ('vishal','cse');
```

Step 5: Create another mysql slave user using:
```bash
CREATE USER 'slave_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password'
```
Grant replication slave for that user by using the below command:
```bash
GRANT REPLICATION SLAVE ON . to `slave_user`@`%` IDENTIFIED BY `password`;
FLUSH PRIVILEGES;
FLUSH TABLES WITH READ LOCK;
```
Check the status of master server's binary log file by using the command:
```bash
SHOW master status;
```
Save Filename and position which is going to be used later on.
Shifted to the second Ubuntu OS which is used to run the slave server.

### CONFIGURE SLAVE

Open the mysql configuration file using the below command
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

* Uncomment server-id and give unique id(2)
* Uncomment log_bin directive to enable binary logging and uncomment binlog_do_db directive and mention the database name which you want to replicate.
* Add relay_log path

Run Mysql server now
```bash
sudo service mysql start
```
Open mysql terminal for root user using:
```bash
sudo mysql -u root -p
```
Create same database  which created in the master server and insert the same data
```bash
CREATE DATABASE students;
USE students;
CREATE TABLE stud (name VARCHAR(20), branch VARCHAR(25));
INSERT INTO stud VALUES ('Ricky',  'EEE');
INSERT INTO stud VALUES ('Vishal', 'Cse');
```
To start replication used the following command in the slave mysql shell:
```bash
CHANGE REPLICATION SOURCE TO
SOURCE_HOST='master_server_ip',
SOURCE_USER='slave_user',
SOURCE_PASSWORD='password',
SOURCE_LOG_FILE= saved_filename,
SOURCE_LOG_POS= previously_saved_postion;
```     
Start replication by using :
```bash
START REPLICA;
```
*TESTING REPLICATION PROCESS*

* In the master mysql server shell, insert data into the table using : 
```bash 
INSERT INTO stud VALUES ('sanjay', 'Cse');
```
* Display the table in the slave mysql server shell using:
```bash 
SELECT * FROM stud;
```
Data added in the master table in the master database will be also added in the slave table in the slave database.
Replication in master slave databases of mysql is done successfully.

# Task - Implement a Client-Server Architecture using MySQL Database Management System (DBMS)

## The following instructions were followed to implement the above task:

### Step 1. Create and configure two linux-based virtual servers (EC2 instance in AWS)

- ```mysql server```
- ```mysql client```

__1.__ Two EC2 Instances of t2.micro type and Ubuntu 24.04 LTS (HVM) was lunched in the us-east-1 region using the AWS console.

![Lunch Instance](./images/create-ec2.png)

__mysql server__
![Lunch Instance](./images/ec2-server-detail.png)

__mysql client__
![Lunch Instance](./images/ec2-client-detail.png)

The security group inbound rule for both instances was configured with the default SSH on port 22 with source from anywhere.

![Lunch Instance](./images/ec2-server-security-rule.png)
![Lunch Instance](./images/ec2-client-security-rule.png)

__2.__ Attached SSH key named __my-ec2-key__ to access the instance on port 22


## Step 2 - On ```mysql server``` Linux Server, install MySQL Server software

__1.__ The private ssh key permission was changed for the private key file and then used to connect to the instance by running

```bash
chmod 400 my-ec2-key.pem
```
```bash
ssh -i "my-ec2-key.pem" ubuntu@3.87.82.40
```
Where __username=ubuntu__ and __public ip address=3.87.82.40__

![Connect to instance](./images/ssh-server.png)

__2.__ __Update and upgrade Ubuntu__

```bash
sudo apt update && sudo apt upgrade -y
```
![Update ubuntu](./images/server-update-upgrade.png)

__3.__ __Install MySQL Server software__

```bash
sudo apt install mysql-server -y
```
![Install mysql](./images/install-mysql-server.png)

__4.__ __Enable mysql server__

```bash
sudo systemctl enable mysql
```
![Enable mysql](./images/enable-mysql.png)


## Step 3 - On ```mysql client``` Linux Server install MySQL Client software.

__1.__ __Connect to the instance__

```bash
ssh -i "my-ec2-key.pem" ubuntu@54.242.30.171
```
Where __username=ubuntu__ and __public ip address=54.242.30.171__

![Connect to instance](./images/ssh-client.png)

__2.__ __Update and upgrade Ubuntu__

```bash
sudo apt update && sudo apt upgrade -y
```
![Update ubuntu](./images/client-update-upgrade.png)

__3.__ __Install MySQL Client software__

```bash
sudo apt install mysql-client -y
```
![Install mysql](./images/install-mysql-client.png)


## Step 4 - Use ```mysql server's``` local IP address to connect from ```mysql client```.

By default, both of the EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using __local IP addresses__. Use ```mysql server's``` local IP address to connect from ```mysql client```. MySQL server uses ```TCP port 3306``` by default so it has to be opened by creating a new entry in __inbound rules__ in __mysql server__ Security Groups.
For extra security, access to __mysql server__ by all IP addresses was not allowed, only the specific local IP address of __mysql client__ was allowed.

![server security rule](./images/server-security-rule2.png)


## Step 5 - Configure MySQL server to allow connections from remote hosts.

__Befor the configuration stated above, the following were implemented:__

__1.__ The security script of MySQL was run on __mysql server__ by running the command:

```bash
sudo mysql_secure_installation
```
![secure mysql](./images/secure-mysql.png)

__2.__ __Access MySQL shell__

```bash
sudo mysql
```
![mysql shell](./images/mysql-shell.png)

__3.__ __On mysql server, create a user named ```client``` and a database named ```test_db```__.

```bash
CREATE USER 'client'@'%' IDENTIFIED WITH mysql_native_password BY 'User123$';

CREATE DATABASE test_db;

GRANT ALL ON test_db.* TO 'client'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;
```

![DB](./images/create-user-n-db.png)

__4.__ __Now, configure MySQL server to allow connections from remote hosts__.

```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
Locate ```bind-address = 127.0.0.1```

Replace ```127.0.0.1``` with ```0.0.0.0```

![command](./images/mysql-bind-command.png)
![bind-address](./images/mysql-bind-address.png)


## Step 6 - From ```mysql client``` Linxus Sever, connect remotely to ```mysql server``` Database Engine without using SSH. The mysql utility must be used to perform this action.

```bash
sudo mysql -u client -h 172.31.18.141 -p
```
![Client to DB](./images/access-db-server-from-client.png)


## Step 7 - Check that the connection to the remote MySQL server was successfull and can perform SQL queries.

```bash
show databases;
```
![show db](./images/show-db.png)


__Create table, insert rows into table and select from the table__

```bash
CREATE TABLE test_db.test_table (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
);

INSERT INTO test_db.test_table (content) VALUES ("My first choice football club is Chelsea");

INSERT INTO test_db.test_table (content) VALUES ("My second choice football club is R.Madrid");

SELECT * FROM test_db.test_table;
```

![Query](./images/sql-query.png)


At this point, this project is successfully complete.
This deployment is a fully functional MySQL Client-Server set up.









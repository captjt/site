---
layout: post
cover: false
title: Creating Master Slave Replication in MySQL on CentOS 7
date:   2016-09-30 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/ghost.png'
---

### Master Server

Edit the my.cnf

~~~
$ sudo vi /etc/my.cnf
~~~
{: .language-bash}

~~~
server-id = 1
binlog_do_db = testdatabase
relay-log = mysql-relay-bin
relay-log-index = mysql-relay-bin.index
log_bin = mysql-bin.log
~~~

Restart mysqld

~~~
$ sudo systemctl restart mysqld
~~~
{: .language-bash}

In mysql

`mysql -u root -p`

~~~
-- create a database to replicate
> CREATE DATABASE testdatabase;
> USE testdatabase;
-- create a table in testdatabase
> CREATE TABLE Users (name varchar(30), age INT);
-- insert data
> INSERT INTO testdatabase.Users Values ('Test Name', 1);
> INSERT INTO testdatabase.Users Values ('Test Name', 2);

--
> GRANT REPLICATION SLAVE ON *.* TO 'slaveuser'@'%' IDENTIFIED BY 'PASSWORD'
> FLUSH PRIVILEGES;
> FLUSH TABLES WITH READ LOCK;
> SHOW MASTER STATUS;

+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |     101 | testdatabase |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
~~~

Make sure the 'PASSWORD' is **secure**

Do a backup to transfer to the slave server

~~~
$ mysqldump -u root -p --master-data testdatabase > /home/centos/database.sql
~~~
{: .language-bash}

Move it to the slave server

~~~
$ rsync -Waq -e ssh /home/centos/database.sql centos@IPADDRESS:/home/centos/
~~~
{: .language-bash}

IPADDRESS is the slave IP

### On Slave Server

Update the my.cnf file

~~~
$ sudo vi /etc/my.cnf
~~~
{: .language-bash}

Add this to the file

~~~
server-id = 2
~~~

Restart mysqld.

~~~
$ sudo systemctl restart mysqld
~~~
{: .language-bash}

Import the database

~~~
$ mysql -u root -p testdatabase < /home/centos/database.sql
~~~
{: .language-bash}

Note. you must have the database 'testdatabase' created.

In mysql shell(`mysql -u root -p`)

~~~
mysql> STOP SLAVE;
mysql> CHANGE MASTER TO
  ->  MASTER_HOST='host-name',
  ->  MASTER_USER='slaveuser',
  ->  MASTER_PASSWORD='PASSWORD',
  ->  MASTER_LOG_FILE='log file from the MASTER STATUS',
  ->  MASTER_LOG_POS=(postion from MASTER STATUS);
mysql> START SLAVE;
mysql> SHOW SLAVE STATUS;
~~~

After this to test it insert some data in the master database and select * from both to check consistency.

Cheers!

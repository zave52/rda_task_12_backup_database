# Backup and restore database

mysqldupm can be a very handy tool, and you will see it while working on this task. You will deal with 3 databases, all hosted on the same server: 
- `ShopDB` - a production database for online shop. This is the database with a real user data. 
- `ShopDBReserve` - a copy of production database, used for a fast disaster recovery. 
- `ShopDBDevelopment` - a database, used by the development team. 

You were tasked to develop an automated procedure, which runs every night. The procedure consist of 2 activities: 

1. Creating a full database backup from `ShopDB` database, and restoring it to `ShopDBReserve` for fast disaster recovery
2. Moving data from production database `ShopDB` to `ShopDBDevelopment` (database schema should not be copied from production to development environment). 

The activities will be execuded on a schedule as a [cron job](https://www.hostinger.com/tutorials/cron-job), and you need to write a bash script, which will be used by a job. 


## Prerequisites

1. Install and configure a MySQL database server on a Virtual Machine.
2. Fork this repository.

## Requirements

In this task, you will need to write a bash script in file `task.sh`. Script should implement a procedure for a backup and restoration of a database, described bellow: 

1. Read database username and password from environment variables DB_USER and DB_PASSWORD. 

2. Create a full backup of the database `ShopDB` using mysqldump, and restore it to the database `ShopDBReserve`. 

3. Create a data backup from the database `ShopDB`, and restore it to the database `ShopDBDevelopment`. 

In order to test this script, you will need to to some preparations: 

1. Connect to your database server, and drop the database `ShopDB` if you have it already. 

2. Create databases, described in the task, using script `create-database.sql` 

3. Create a database user, which has just enough permissions to complete required actions: 
```
CREATE USER 'backup'@'%' IDENTIFIED BY '<password>';

-- configure minimum requored permissions for performing a db backup using mysqldump: 
GRANT SELECT, LOCK TABLES, SHOW VIEW, PROCESS ON *.* TO 'backup'@'%';

-- configure required permissions to restore a database 
GRANT ALL ON ShopDBReserve.* TO 'backup'@'%';

-- configure required permissions to restore only data
GRANT INSERT, LOCK TABLES, ALTER ON ShopDBDevelopment.* TO 'backup'@'%';
```

4. Connect to your database server using SSH, and export environment variables with database credentials: 
```
export DB_USER="backup"
export DB_PASSWORD="<db-password>"
```

5. Run your script without arguments. 

6. Query the data in the databases: table `Products` should be presend in both `ShopDBReserve` and `ShopDBDevelopment` databases, and it should have same amount of recotrs as in the  `ShopDB` database
## About Percona MySQL

Percona Server is an enhanced, drop-in MySQL replacement. With Percona Server:
  - Queries will run faster and more consistently
  - You can consolidate servers on powerful hardware
  - Sharding is delayed or avoided entirely
  - You can save money on hosting fees and power
  - You can spend less time tuning and administering
  - You can achieve higher uptime
  - Troubleshooting does not require guesswork

## Set Up

The steps in this tutorial require the user to have root privileges on your VPS. You can see how to set that up in the [Initial Server Setup](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04) in steps 3 and 4.

## Step One — Add Percona's repositories and key

  - Open terminal and execute the following command to add the repository

`sudo echo "deb http://repo.percona.com/apt precise main" > /etc/apt/sources.list.d/percona.list` - This command is for Ubuntu 12.04 (precise). To use another distributon - see supported distributions and their named versions [here](http://www.percona.com/doc/percona-server/5.5/installation/apt_repo.html).

  - Add Percona's signing key

`sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 1C4CBDCDCD2EFD2A`

  - Update packages

`sudo apt-get update`

## Step Two — Install server and client

  - Open terminal and execute the following command to install Percona MySQL (stable)

`sudo apt-get install percona-server-server-5.5 percona-server-client-5.5` - Installing the client is optional if this droplet is going to be a dedicated MySQL server.

  - You can also install percona using its virtual package. The virtual package has same update properties as stable. But once testing (currently 5.6 RC) becomes stable the virtual package will point to it, leading to possible conflicts and issues when upgrading! To install virtual package -

`sudo apt-get install percona-server-server percona-server-client`

  - During the installation you will be prompted to set a password for MySQL's root user.
  - Once complete you should see similar to

~~~~
[ ok ] Stopping MySQL (Percona Server): mysqld.
130812 19:00:25 [Note] Plugin 'FEDERATED' is disabled.
130812 19:00:25 InnoDB: The InnoDB memory heap is disabled
130812 19:00:25 InnoDB: Mutexes and rw_locks use GCC atomic builtins
130812 19:00:25 InnoDB: Compressed tables use zlib 1.2.3
130812 19:00:25 InnoDB: Using Linux native AIO
130812 19:00:25 InnoDB: Initializing buffer pool, size = 128.0M
130812 19:00:25 InnoDB: Completed initialization of buffer pool
130812 19:00:25 InnoDB: highest supported file format is Barracuda.
130812 19:00:25  InnoDB: Waiting for the background threads to start
130812 19:00:26 Percona XtraDB (http://www.percona.com) 5.5.32-rel31.0 started; log sequence number 1597945
130812 19:00:26  InnoDB: Starting shutdown...
130812 19:00:30  InnoDB: Shutdown completed; log sequence number 1597945


 * Percona Server is distributed with several useful UDF (User Defined Function) from Percona Toolkit.
 * Run the following commands to create these functions:

  mysql -e "CREATE FUNCTION fnv1a_64 RETURNS INTEGER SONAME 'libfnv1a_udf.so'"
    mysql -e "CREATE FUNCTION fnv_64 RETURNS INTEGER SONAME 'libfnv_udf.so'"
    mysql -e "CREATE FUNCTION murmur_hash RETURNS INTEGER SONAME 'libmurmur_udf.so'"

 * See http://www.percona.com/doc/percona-server/5.5/management/udf_percona_toolkit.html for more details


[ ok ] Starting MySQL (Percona Server) database server: mysqld ..
[info] Checking for corrupt, not cleanly closed and upgrade needing tables..
~~~~

## Step Three — Test connection

  - Open terminal and enter. When prompted for password use the one you set up earlier

`mysql -u root -p`

  - The output on the screen should be similar to

~~~~
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 43
Server version: 5.5.32-31.0 Percona Server (GPL), Release 31.0

Copyright (c) 2009-2013 Percona Ireland Ltd.
Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
~~~~

## Step Four — Simple configuration

Percona's MySQL **DOES NOT** come with `my.cnf`. So you need to create it. When creating or altering the contents of `my.cnf` your server may not start. There could be many reasons for that. One of the more common is change of InnoDB log files size - To fix it stop MySQL, remove the log files and restart MySQL. The InnoDB logfiles will be recreated.

  - First stop MySQL `sudo /etc/init.d/mysql stop` or `sudo service mysql stop`
  - Remove old InnoDB's logs `sudo rm /var/lib/mysql/ib_logfile*`
  - Open `/etc/mysql/my.cnf` with your editor of choice and paste the following configuration. It is designed around the 512MB ram Droplets

~~~~
[mysql]

# CLIENT #
port                           = 3306

[mysqld]

# GENERAL #
user                           = mysql
default_storage_engine         = InnoDB
pid_file                       = /var/lib/mysql/mysql.pid

# MyISAM #
key_buffer_size                = 32M
myisam_recover                 = FORCE,BACKUP

# SAFETY #
max_allowed_packet             = 16M
max_connect_errors             = 1000000
sql_mode                       = STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY
sysdate_is_now                 = 1
innodb                         = FORCE

# DATA STORAGE #
datadir                        = /var/lib/mysql/

# BINARY LOGGING #
log_bin                        = /var/lib/mysql/mysql-bin
expire_logs_days               = 14
sync_binlog                    = 1

# CACHES AND LIMITS #
tmp_table_size                 = 32M
max_heap_table_size            = 32M
query_cache_type               = 0
query_cache_size               = 0
max_connections                = 500
thread_cache_size              = 50
open_files_limit               = 65535
table_definition_cache         = 4096
table_open_cache               = 4096

# INNODB #
innodb_flush_method            = O_DIRECT
innodb_log_files_in_group      = 2
innodb_log_file_size           = 32M
innodb_flush_log_at_trx_commit = 1
innodb_file_per_table          = 1
innodb_buffer_pool_size        = 256M

# LOGGING #
log_error                      = /var/lib/mysql/mysql-error.log
log_queries_not_using_indexes  = 1
slow_query_log                 = 1
slow_query_log_file            = /var/lib/mysql/mysql-slow.log
~~~~

  - Restart MySQL `sudo /etc/init.d/mysql start` or `sudo service mysql start`


## Success — You have installed Percona MySQL Server

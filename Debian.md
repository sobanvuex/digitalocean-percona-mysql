### About Percona MySQL

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

## Step Oen — Add Percona's repositories and key

  - Open terminal and execute the following command to add the repository

```sudo echo "deb http://repo.percona.com/apt precise main" > /etc/apt/sources.list.d/percona.list``` - This command is for Ubuntu 12.04 (precise). To use another distributon - see supported distributions and their named versions [here](http://www.percona.com/doc/percona-server/5.5/installation/apt_repo.html).

  - Add Percona's signing key

```sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 1C4CBDCDCD2EFD2A```

  - Update packages

```sudo apt-get update```

## Step Two — Install server and client

  - Open terminal and execute the following command to install Percona MySQL (stable)

```sudo apt-get install percona-server-server-5.5 percona-server-client-5.5``` - Installing the client is optional if this droplet is going to be a dedicated MySQL server.

  - You can also install percona using its virtual package. The virtual package has same update properties as stable. But once testing (currently 5.6 RC) becomes stable the virtual package will point to it, leading to possible conflicts and issues when upgrading! To install virtual package -

```sudo apt-get install percona-server-server percona-server-client```

  - During the installation you will be prompted to set a password for MySQL's root user.
  - Once complete you should see similar to

```
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
```

## Step Three — Test connection

  - Open terminal and enter. When prompted for password use the one you set up earlier

```mysql -u root -p```

  - The output on the screen should be similar to

```
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
```
### Success - You have installed Percona MySQL Server

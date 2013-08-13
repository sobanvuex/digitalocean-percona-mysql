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

The steps in this tutorial require the user to have root privileges on your VPS. You can see how to set that up in the [Initial Server Setup](https://www.digitalocean.com/community/articles/initial-server-setup-with-centos-6) in steps 3 and 4.

## Step Oen — Add Percona's repositories and key

  - Open terminal and execute the following command to add the repository

```sudo rpm -Uhv http://www.percona.com/downloads/percona-release/percona-release-0.0-1.x86_64.rpm``` - This command is for 64-bit operating system. To install 32-bit version - replace `x86_64` with `i386 `.

  - Test the repository

```yum list | grep percona``` - You should see an output similar to

```
Percona-SQL-50-debuginfo.x86_64          5.0.92-b23.89.rhel6           percona  
Percona-SQL-client-50.x86_64             5.0.92-b23.89.rhel6           percona  
Percona-SQL-devel-50.x86_64              5.0.92-b23.89.rhel6           percona  
...
percona-xtrabackup-debuginfo.x86_64      2.1.4-656.rhel6               percona  
percona-xtrabackup-test.x86_64           2.1.4-656.rhel6               percona  
qpress.x86_64                            11-1.el6                      percona  
qpress-debuginfo.x86_64                  11-1.el6                      percona
```

## Step Two — Install server and client

  - Open terminal and execute the following command to install Percona MySQL (stable)

```sudo yum install Percona-Server-server-55 Percona-Server-client-55``` - Installing the client is optional if this droplet is going to be a dedicated MySQL server.

  - During the installation you may be prompted to accept Percona's repository signing key

```
warning: rpmts_HdrFromFdno: Header V4 DSA/SHA1 Signature, key ID cd2efd2a: NOKEY
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-percona
Importing GPG key 0xCD2EFD2A:
 Userid : Percona MySQL Development Team <mysql-dev@percona.com>
 Package: percona-release-0.0-1.x86_64 (installed)
 From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-percona
Is this ok [y/N]: y
```

  - Once complete you should see similar to

```
  Installing : Percona-Server-shared-55-5.5.32-rel31.0.549.rhel6.x86_64                                                       1/12 
  Installing : 1:perl-Pod-Escapes-1.04-131.el6_4.x86_64                                                                       2/12 
  Installing : 4:perl-libs-5.10.1-131.el6_4.x86_64                                                                            3/12 
  Installing : 3:perl-version-0.77-131.el6_4.x86_64                                                                           4/12 
  Installing : 1:perl-Module-Pluggable-3.90-131.el6_4.x86_64                                                                  5/12 
  Installing : 1:perl-Pod-Simple-3.13-131.el6_4.x86_64                                                                        6/12 
  Installing : 4:perl-5.10.1-131.el6_4.x86_64                                                                                 7/12 
  Installing : Percona-Server-client-55-5.5.32-rel31.0.549.rhel6.x86_64                                                       8/12 
  Installing : libaio-0.3.107-10.el6.x86_64                                                                                   9/12 
  Installing : Percona-Server-server-55-5.5.32-rel31.0.549.rhel6.x86_64                                                      10/12 

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h percona-mysql-rpm password 'new-password'

Alternatively you can run:
/usr/bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

Please report any problems with the /usr/bin/mysqlbug script!

Percona recommends that all production deployments be protected with a support
contract (http://www.percona.com/mysql-suppport/) to ensure the highest uptime,
be eligible for hot fixes, and boost your team's productivity.
Percona Server is distributed with several useful UDF (User Defined Function) from Maatkit.
Run the following commands to create these functions:
mysql -e "CREATE FUNCTION fnv1a_64 RETURNS INTEGER SONAME 'libfnv1a_udf.so'"
mysql -e "CREATE FUNCTION fnv_64 RETURNS INTEGER SONAME 'libfnv_udf.so'"
mysql -e "CREATE FUNCTION murmur_hash RETURNS INTEGER SONAME 'libmurmur_udf.so'"
See http://code.google.com/p/maatkit/source/browse/trunk/udf for more details
  Installing : Percona-Server-shared-compat-5.5.32-rel31.0.549.rhel6.x86_64                                                  11/12 
...
  Verifying  : mysql-libs-5.1.69-1.el6_4.x86_64                                                                              12/12 

Installed:
  Percona-Server-client-55.x86_64 0:5.5.32-rel31.0.549.rhel6     Percona-Server-server-55.x86_64 0:5.5.32-rel31.0.549.rhel6        
  Percona-Server-shared-55.x86_64 0:5.5.32-rel31.0.549.rhel6     Percona-Server-shared-compat.x86_64 0:5.5.32-rel31.0.549.rhel6    

Dependency Installed:
  libaio.x86_64 0:0.3.107-10.el6            perl.x86_64 4:5.10.1-131.el6_4           perl-Module-Pluggable.x86_64 1:3.90-131.el6_4 
  perl-Pod-Escapes.x86_64 1:1.04-131.el6_4  perl-Pod-Simple.x86_64 1:3.13-131.el6_4  perl-libs.x86_64 4:5.10.1-131.el6_4           
  perl-version.x86_64 3:0.77-131.el6_4     

Replaced:
  mysql-libs.x86_64 0:5.1.69-1.el6_4                                                                                               

Complete!
```

## Step Three — Initial setup

  - During installation you are not prompted to enter root password - leaving your server vulnerable. Instead Percona has provided a script to complete the installation
  - First start the server `sudo /etc/init.d/mysql start`
  - Run the secure installation script `sudo /usr/bin/mysql_secure_installation`. You will be aksed a series of questions -
  - The **current** root password (blank/none) 

```Enter current password for root (enter for none):```

  - Set **new** root password

```
Set root password? [Y/n] y
New password: 
Re-enter new password:
```

  - Remove MySQL's anonymous users

```
Remove anonymous users? [Y/n] y
```

  - Disallow remote (other than localhost) root login

```
Disallow root login remotely? [Y/n] y
```

  - Remote test database

```
Remove test database and access to it? [Y/n] y
```

  - Reload privileges

```
Reload privilege tables now? [Y/n] y
```

## Step Four — Test connection

  - Open terminal and enter. When prompted for password use the one you set up earlier

```mysql -u root -p```

  - The output on the screen should be similar to

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 5.5.32-31.0 Percona Server (GPL), Release rel31.0, Revision 549

Copyright (c) 2009-2013 Percona Ireland Ltd.
Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## Step Five — Simple configuration

Percona's MySQL comes with a **LIMITED** `my.cnf`. It is advised to modify it. When creating or altering the contents of `my.cnf` your server may not start. There could be many reasons for that. One of the more common is change of InnoDB log files size - To fix it stop MySQL, remove the log files and restart MySQL. The InnoDB logfiles will be recreated.

  - First stop MySQL `sudo /etc/init.d/mysql stop` or `sudo service mysql stop`
  - Remove old InnoDB's logs `sudo rm /var/lib/mysql/ib_logfile*`
  - Open `/etc/my.cnf` with your editor of choice and paste the following configuration. It is designed around the 512MB ram Droplets

```
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
```

  - Restart MySQL `sudo /etc/init.d/mysql start` or `sudo service mysql start`

## Success  — You have installed Percona MySQL Server

# Dokumentasi CentOS 9

## PHP
update system
```
sudo dnf update
```
install repo remi
```
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
```
list PHP version
```
sudo dnf module list php
```
enable PHP version
```
sudo dnf module reset php
sudo dnf module install -y php:remi-8.3
```
install PHP 8.3
```
sudo dnf install php php-cli php-fpm php-mysqlnd php-xml php-mbstring php-json php-zip -y
```
chech PHP version
```
php -v
```

## HTTPD
update system
```
sudo dnf update
```
install httpd
```
sudo dnf install httpd -y
```
enable httpd
```
sudo systemctl enable --now httpd
```
check status
```
sudo systemctl status httpd
```
allow http
```
sudo firewall-cmd --add-service=http --permanent
```
allow https
```
sudo firewall-cmd --add-service=https --permanent
```
reload firewalld
```
sudo firewall-cmd --reload
```

# mariadb-server
install mariadb
```
sudo dnf install mariadb-server -y
```
enable mariadb
```
sudo systemctl enable --now mariadb
```
setup mariadb
```
[kurokawa@kurokawa ~]$ mariadb-secure-installation 

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```
login mariadb
```
sudo mariadb -u username -p
```
create user
```
CREATE USER 'user_baru'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```
create database
```
MariaDB [(none)]> CREATE DATABASE laranime;
Query OK, 1 row affected (0.001 sec)
```
list database
```
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| db_laranime        |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.001 sec)
```
select database
```
MariaDB [(none)]> USE laranime
```
create table
```
MariaDB [laranime]> CREATE TABLE list_husbu (
    -> id INT PRIMARY KEY AUTO_INCREMENT,
    -> name VARCHAR(255),
    -> uid VARCHAR(20),
    -> profesi VARCHAR(100)
    -> );
Query OK, 0 rows affected (0.005 sec)
```
list table
```
MariaDB [laranime]> show tables;
+-------------------+
| Tables_in_laranime |
+-------------------+
| list_husbu         |
+-------------------+
1 row in set (0.001 sec)
```
insert data
```
MariaDB [laranime]> INSERT INTO list_husbu (username, uid, role) VALUES
    -> ('amuro', '00001', 'police'),
    -> ('conan', '00002', 'detective');
Query OK, 2 rows affected (0.004 sec)
Records: 2  Duplicates: 0  Warnings: 0
```
dump data table
```
MariaDB [laranime]> SELECT * FROM list_users;
+----+----------+-------+-------------+
| id | username | uid   | profesi     |
+----+----------+-------+-------------+
|  1 | amuro    | 00001 | police      |
|  2 | conan    | 00002 | detective   |
+----+----------+-------+-------------+
2 rows in set (0.001 sec)

MariaDB [laranime]> SELECT * FROM laranime WHERE role = 'detective';
+----+----------+-------+-----------+
| id | username | uid   | profesi   |
+----+----------+-------+-----------+
|  2 | conan    | 00002 | detective |
+----+----------+-------+-----------+
1 rows in set (0.001 sec)
```
# Backup & Restore (ISSUE on Physical)
## Logical Backup & Restore
Backup
```
[kurokawa@kurokawa ~]$ sudo mariadb-dump laranime > laranime.sql
```
Restore
```
[kurokawa@kurokawa ~]$ sudo mariadb-dump laranime < laranime.sql
```

## Physical Backup & Restore
Backup
```
[kurokawa@kurokawa ~]$ sudo mariabackup --backup \
   --target-dir=/var/mariadb/backup/ \
   --user=root --password=asrul
```
Restore
```
[kurokawa@kurokawa ~]$ sudo mariabackup --prepare \
   --target-dir=/var/mariadb/backup/
```
```
[kurokawa@kurokawa ~]$ sudo mariabackup --copy-back \
   --target-dir=/var/mariadb/backup
```
   
## PHPMyAdmin
enable repo crb
```
sudo dnf config-manager --set-enabled crb
```

install repo epel
```
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-next-release-latest-9.noarch.rpm
```

install PHPMyAdmin
```
dnf install -y phpmyadmin
```

Add the "Require all granted" line under the "Require local" line
```
Alias /phpMyAdmin /usr/share/phpMyAdmin
Alias /phpmyadmin /usr/share/phpMyAdmin

<Directory /usr/share/phpMyAdmin/>
   AddDefaultCharset UTF-8
   Require local
   Require all granted   # tambahkan disini
</Directory>

<Directory /usr/share/phpMyAdmin/setup/>
   Require local
</Directory>
```

restart httpd
```
systemctl restart httpd
```

login PHPMyAdmin
```
http://localhost/phpmyadmin
```

## SELinux
menampilkan daftar port yang dikonfigurasi dalam SELinux
```
sudo semanage port -l | grep ssh
```

Menambah port 2222 ke kebijakan SELinux:
```
sudo semanage port -a -t ssh_port_t -p tcp 2222
```

## SSH
restart SSH
```
sudo systemctl restart sshd
```

uji coba login SSH
```
ssh username@<IP Your SSH> -p <Port your SSH>
```


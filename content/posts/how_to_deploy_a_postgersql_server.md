---
date: '2025-07-16T00:23:54+05:30'
date: '2025-08-04T23:00:00+05:30'
draft: false
title: 'How to deply a postgresql server'
math: true
author: abdul
---


### Database Cluster

A database cluster is a collection of databases that is managed by a
single instance of a running database server. In this post, we will talk
about how to create a database cluster. 

### Prerequisites

The data created by postgresql is stored on the host file system in a
directory called **data directory**. You can choose any directory to be
data directory for example: `/usr/local/pgsql/data` or
`/var/lib/pgsql/data`. The data directory is initialized using a program
called `initdb` which comes with standard `postgresql-server`
installation.

### Installing postgresql-server

Almost all linux systems provide a package called `postgresql-server` or
a package with similar name. You can check the exact name by searching
package repository for your distribution. For fedora, it is
`postgresql-server` which can be installed by running:

```sh
sudo dnf install postgresql-server
```

### Initializing the data directory

The data directory can be intialized using
[initdb](https://www.postgresql.org/docs/current/app-initdb.html)
program. But before running the command, there are a few important
points to remember:

- The data must be owned by a particular `user` that is not `root`.
  Therefore it is advisable to create a new user. The postgresql
  installation automatically creates a user called `postgres` which can
  be used for the purpose.
- The data directory must be owned by the user you create. And all
  commands to control the server i.e. `pg_ctl` commands, must be issued
  using the special user you create.

For this blog post, we will assume that you are going to use `postgres`
user created by default, and use the directory `/usr/local/psql/data` as
data directory.

### Creating the data directory

Initially, the `postgres` user will not have permission to create a
directory. Therefore, you first need to create the data directory and
then change it's owner to `postgres`

```sh
sudo mkdir -p /usr/local/psql/data
sudo chown postgres /usr/local/psql/data
```

After this, initialize the data directory using:

```sh
sudo -u postgres initdb -D /usr/local/psql/data
```

### Start the server

Now you are all set. Start the server using:

```sh
sudo -u postgres pg_ctl start -D /usr/local/psql/data # or
# providing a log file
sudo -u postgres pg_ctl start -D /usr/local/psql/data -l /path_log_file
```

Please take special care that all commands must be issued by the
`postgres` user, using `sudo -u postgres [command]`.


### Testing and Using postgresql (Optional)

After you have successfully fired up the postgresql server using the
above instructions. You might want to test it.

For the purpose of using the `postgresql-server` you need a **postgresql
client**. You can use
[`psql`](https://www.postgresql.org/docs/current/app-psql.html), which
is a terminal client for interacting with postgresql server. Most
distributions provide a plain `postgersql` package which contains this
binary. On fedora, simply run:

```sh
sudo dnf install postgresql
```

This will give you the psql command.

``sh
man psql
``

After, installing `psql`, now you need to log into your `postgres` user
account and start `psql`. It is also recommended that you change the
password for the `postgres` user as per your liking.

```sh
$ sudo passwd postgres
password: <enter a new password>
```

Then simply log into the `postgres` user account.

```sh
$ su postgresql
bash-5.2$ psql
postgres=#
```

You are now logged into the `psql` shell. Use `\l` to see available
databases.

```sh
postgres=# \l
                                                       List of databases
   Name    |  Owner   | Encoding | Locale Provider |   Collate   |    Ctype    | ICU Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+-------------+-------------+------------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           |
 template0 | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | =c/postgres          +
           |          |          |                 |             |             |            |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | =c/postgres          +
           |          |          |                 |             |             |            |           | postgres=CTc/postgres
(3 rows)
```

By default, you get these 3 databases. You can run queries and play with
the data.



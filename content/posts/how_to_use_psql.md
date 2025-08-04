---
date: '2025-08-05T00:00:00+05:30'
draft: false
title: 'How to use psql'
math: true
author: abdul
---

### Launching psql session


In my previous blog, I described how you can set up a **postgresql
server**. In this post, we will learn, what are some of the basic
commands in `psql` and how can we efficiently interact with it. 

To launch the `psql` terminal interface, you need to log into the
`postgres` user or the `username` that created the `postgresql server`.
This can be done using the `su` command.

```sh
{04 Aug 23:33}~ ➭ su postgres
Password: 
bash-5.2$ psql
psql (16.8)
Type "help" for help.

postgres=# \l
                                                         List of databases
     Name      |  Owner   | Encoding | Locale Provider |   Collate   |    Ctype    | ICU Locale | ICU Rules |   Access privileges   
---------------+----------+----------+-----------------+-------------+-------------+------------+-----------+-----------------------
 postgres      | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | 
 template0     | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | =c/postgres          +
               |          |          |                 |             |             |            |           | postgres=CTc/postgres
 template1     | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | =c/postgres          +
(4 rows)

postgres=# 
```

By default there are 3 databases that are already created by the default
installation and you are connected to `postgres` database. You can
change the database using the command:

```sh
postgres=# \connect template1
You are now connected to database "template1" as user "postgres".
template1=#
```

Here we used the `\connect` command to connect to database "template1".
You might be curious, what's inside of "template1". Let's try listing
it's tables:


```sh
template1-# \dt
Did not find any relations.
```

We see that there's nothing in "template1" database. No worries, let's
create our own database called test database.

```sh
template1=# CREATE DATABASE test_database;
template1=# \connect test_database
You are now connected to database "test_database" as user "postgres".
```

Create some tables, and insert data.

```sh
test_database=# CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

test_database=# CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    stock_quantity INTEGER DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE
);

test_database=# INSERT INTO users (username, email)
test_database-# VALUES ('Abdul', 'abdul@gmail.com');

test_database=# SELECT * FROM users;
 id | username |      email      |            created_at
----+----------+-----------------+----------------------------------
  1 | Abdul    | abdul@gmail.com | 2025-08-04 23:49:42.818163+05:30
(1 row)

test_database=# INSERT INTO products (name, price, stock_quantity, is_active)
VALUES ('bugati', 700000, 23, true),
       ('mazerati', 30000, 213, true);
INSERT 0 2

test_database=# SELECT * FROM products;
 product_id |   name   |   price   | stock_quantity | is_active
------------+----------+-----------+----------------+-----------
          1 | bugati   | 700000.00 |             23 | t
          2 | mazerati |  30000.00 |            213 | t
(2 rows)
```

Here, we need to note that `id` and `product_id` are SERIAL fields which
increment automatically. Therefore, we do not need to explicitly set
them. 


Here we created 2 tables: "users" and "products" and inserted data into
them. Now let's see, what are the tables available inside of our
`test_database` from `psql` terminal.

```sh
test_database=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner
--------+----------+-------+----------
 public | products | table | postgres
 public | users    | table | postgres
(2 rows)
```

Now let's try to check which columns are available in the users table.

```sh
test_database-# \d users
                                       Table "public.users"
   Column   |           Type           | Collation | Nullable |              Default
------------+--------------------------+-----------+----------+-----------------------------------
 id         | integer                  |           | not null | nextval('users_id_seq'::regclass)
 username   | character varying(50)    |           | not null |
 email      | character varying(100)   |           | not null |
 created_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)
    "users_email_key" UNIQUE CONSTRAINT, btree (email)
    "users_username_key" UNIQUE CONSTRAINT, btree (username)

test_database-# \d products
                                            Table "public.products"
     Column     |          Type          | Collation | Nullable |                   Default
----------------+------------------------+-----------+----------+----------------------------------------------
 product_id     | integer                |           | not null | nextval('products_product_id_seq'::regclass)
 name           | character varying(100) |           | not null |
 price          | numeric(10,2)          |           | not null |
 stock_quantity | integer                |           |          | 0
 is_active      | boolean                |           |          | true
Indexes:
    "products_pkey" PRIMARY KEY, btree (product_id)
```

Hence, we can list the columns using the `\d table_name` command.


### Conclusion

Thank you for reading the article. These are all the basics that you
need to get started with `psql`. For detailed information, I would
recommend you to check out [`postgresql's official
documentation`](https://www.postgresql.org/docs/current/app-psql.html).

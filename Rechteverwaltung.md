## Roles and Schema

In a PostgreSQL server, a role is a collection of permissions that you can assign to one or more users.
Roles simplify assigning bundled privileges to users in a multi-user environment using a single statement.

One great example of a scenario where you can use database roles is an e-commerce application to process orders.
In this scenario, you have three roles: system administrators, order specialists, and customer support.

- System administrators can create, view, update, and delete orders.
- Order specialists can create and view the orders, but they can not delete or update them.
- Customer support staff can only view the order details, but they can not change or create new orders.

In the above example, assume you have three store administrators, seven order specialists, and 15 customer support
staff in your company. Without roles, you would have to manually assign the permissions to each user. However, with
the role-based model, you only need to create three roles to assign privileges to the users.

### Create a Sample Database and a Table

```python
$ sudo -u postgres psql

postgres=# CREATE DATABASE store_db;

postgres=# \c store_db;

store_db=# CREATE TABLE orders (
                order_id SERIAL PRIMARY KEY,
                customer_name VARCHAR (50),
                product_name VARCHAR (50),
                amount NUMERIC(5,2)  
            );

store_db=# INSERT INTO orders (customer_name, product_name, amount)
            VALUES ('JOHN DOE', 'BASIC MEMBERSHIP', 5.25);

            INSERT INTO orders (customer_name, product_name, amount)
            VALUES ('PETER ERIC', 'ELITE MEMBERSHIP', 25.25);

            INSERT INTO orders (customer_name, product_name, amount)
            VALUES ('MARY SMITH', 'PREMIUM MEMBERSHIP', 75.25);

store_db=# SELECT
                order_id,
                customer_name, 
                product_name, 
                amount
            FROM orders;

order_id | customer_name |    product_name    | amount
 ----------+---------------+--------------------+--------
         1 | JOHN DOE      | BASIC MEMBERSHIP   |   5.25
         2 | PETER ERIC    | ELITE MEMBERSHIP   |  25.25
         3 | MARY SMITH    | PREMIUM MEMBERSHIP |  75.25
 (3 rows)
```

### Create PostgreSQL Roles and Permissions

The PostgreSQL server treats roles as entities that can own database objects and permissions.
Therefore, every role you create in the PostgreSQL database server is valid across all databases.

```python
# CREATE ROLE EXAMPLE_ROLE_NAME WITH SAMPLE_OPTIONS

store_db=# CREATE ROLE STORE_ADMIN;
store_db=# CREATE ROLE ORDER_SPECIALIST;
store_db=# CREATE ROLE CUSTOMER_SUPPORT;

store_db=#  \du

     Role name     |                         Attributes                         | Member of
 ------------------+------------------------------------------------------------+-----------
  customer_support | Cannot login                                               | {}
  order_specialist | Cannot login                                               | {}
  postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
  store_admin      | Cannot login                                               | {}
```

With the system roles created, you'll grant permissions Using the following syntax.

```python
# GRANT SAMPLE_LIST_OF_PERMISSIONS ON SAMPLE_TABLE_NAME TO EXAMPLE_ROLE_NAME;

store_db=# GRANT INSERT, SELECT, UPDATE, DELETE ON orders TO STORE_ADMIN;

store_db=# GRANT USAGE, SELECT ON SEQUENCE orders_order_id_seq TO STORE_ADMIN;

store_db=# GRANT INSERT, SELECT ON orders TO ORDER_SPECIALIST;
            GRANT USAGE, SELECT ON SEQUENCE orders_order_id_seq TO ORDER_SPECIALIST;

store_db=# GRANT SELECT ON orders TO CUSTOMER_SUPPORT;

store_db=# \z

                                    Access privileges
  Schema |        Name         |   Type   |      Access privileges       | Column privileges | Policies
 --------+---------------------+----------+------------------------------+-------------------+----------
  public | orders              | table    | postgres=arwdDxt/postgres   +|                   |
         |                     |          | store_admin=arwd/postgres   +|                   |
         |                     |          | order_specialist=ar/postgres+|                   |
         |                     |          | customer_support=r/postgres  |                   |
  public | orders_order_id_seq | sequence | postgres=rwU/postgres       +|                   |
         |                     |          | store_admin=rU/postgres     +|                   |
         |                     |          | order_specialist=rU/postgres |                   |
 (2 rows)

# associated permissions denoted with a(INSERT/APPEND), r(SELECT/READ), w(UPDATE/WRITE), d(DELETE), and U(USAGE).
```

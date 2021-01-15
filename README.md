# PostgreSQL cluster with repmgr and pgbouncer 
* Vagrant (Ubuntu 18.04.5 LTS Bionic)
* PostgreSQL
* repmgr
* pgBouncer

## Usage
```sh
vagrant up
```

## Test pgbouncer function and repmgr standby switchover
```sh
# Login to standby node
vagrant ssh node2

# Check PG cluster status (primary is node1)
sudo -u postgres repmgr cluster show
 ID | Name  | Role    | Status    | Upstream | Location | Priority | Timeline | Connection string
----+-------+---------+-----------+----------+----------+----------+----------+---------------------------------------------------------------
 1  | node1 | primary | * running |          | default  | 100      | 3        | host=172.16.1.11 dbname=repmgr user=repmgr  connect_timeout=2
 2  | node2 | standby |   running | node1    | default  | 100      | 2        | host=172.16.1.12 dbname=repmgr user=repmgr  connect_timeout=2
 3  | node3 | witness | * running | node1    | default  | 0        | n/a      | host=172.16.1.13 dbname=repmgr user=repmgr  connect_timeout=2

# Check that pgbouncer is pointing to primary (node1 IP)
PGUSER=temp_user PGPASSWORD=temp_pass PGPORT=6432 psql temp_db

temp_db=> SELECT CURRENT_USER usr, inet_server_addr() host, inet_server_port() port;
    usr    |    host     | port
-----------+-------------+------
 temp_user | 172.16.1.11 | 5432

# Switch Postgres primary node [node1 -> node2]
## Dry-run switch test if everything is OK
# sudo -u postgres repmgr standby switchover -f /etc/repmgr.conf --siblings-follow --dry-run
## Standby switchover
sudo -u postgres repmgr standby switchover -f /etc/repmgr.conf --siblings-follow

# Now pgbouncer is pointing to new primary (node2 local IP)
PGUSER=temp_user PGPASSWORD=temp_pass PGPORT=6432 psql temp_db

temp_db=> SELECT CURRENT_USER usr, inet_server_addr() host, inet_server_port() port;
    usr    |   host    | port
-----------+-----------+------
 temp_user | 127.0.2.1 | 5432
 ```

## Links
* [https://blog.raveland.org/post/postgresql_repmgr_pgbouncer_en](https://blog.raveland.org/post/postgresql_repmgr_pgbouncer_en/)
* [https://repmgr.org/docs/repmgr.html](https://repmgr.org/docs/repmgr.html)

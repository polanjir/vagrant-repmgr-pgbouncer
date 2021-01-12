# PostgreSQL repmgr + pgbouncer Vagrant

* Vagrant (Ubuntu 18.04.5 LTS Bionic)
* PostgreSQL
* repmgr
* pgBouncer

## Usage
```sh
vagrant up
```

## Test repmgr standby switchover
```sh
# Switch Postgres primary node
sudo -u postgres repmgr standby switchover -f /etc/repmgr.conf --siblings-follow

# Check host on all nodes (node1,node2,node3)
PGUSER=temp_user PGPORT=6432 psql temp_db

temp_db=> SELECT CURRENT_USER usr, inet_server_addr() host, inet_server_port() port;
    usr    |   host    | port
-----------+-----------+------
 temp_user | 127.0.2.1 | 5432
 ```

## Links
* [https://blog.raveland.org/post/postgresql_repmgr_pgbouncer_en](https://blog.raveland.org/post/postgresql_repmgr_pgbouncer_en/)
* [https://repmgr.org/docs/repmgr.html](https://repmgr.org/docs/repmgr.html)

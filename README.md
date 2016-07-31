#### pkpsqlrepli2ed
######Different types of replication
Logical versus physical replication (need to memorize)
#####Chapter 2. Understanding the PostgreSQL Transaction Log
######How PostgreSQL writes data
```
cd /var/lib/postgres/9.5/main
vim PG_VERSION
```
select oid
```
SELECT oid, datname FROM pg_database;
```
table=relname
```
create table ke(id int4);
select relfilenode,relname from pg_class where relname='ke';
```
files in /var/lib/postgresql/9.5/main:
```
global
pg_clog - commit log
pg_dynshmem - shared memory
pg_logical - logical decoding
pg_notify - listen/notify
pg_replslot -replication slots
pg_serial serialized transactions
pg_snapshot - exported snapshots
pg_stat perm stat
pg_stat_tmp - temp stats data
pg_subtrans subtransaction
pg_tblspc - symbolic links to tablespace
pg_twophase - prepared statements
pg_xlog - transaction log -write ahead log WAL
```
new added after 9.2
```
pg_commit_ts
pg_multixact
```


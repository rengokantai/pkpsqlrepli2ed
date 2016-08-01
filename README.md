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
WAL: before actually to do underlying table, we make log entries in a sequential order

######Understanding consistency and data loss
```
#fsync = on
#synchronous_commit = on
```
######Tuning checkpoints and the XLOG
shared_buffers, wal_buffers  
wal_buffers how much memory to keep around to remember the XLOG that has not been written to the disk so far. 
default= -1, (take three percent of shared_buffers)

######Experiencing the XLOG in action
The size of the postgres transaction log
```
(2* checkpoint_completion_target)*checkpoint_segments+1
checkpoint_segments+wal_keep_segments+1
```

complie postgres from source,then
```
vim src/include/pg_config_manual.h
```
uncomment
```
WAL_DEBUG
````
then
```
# SET client_min_messages TO log;
# SET wal_debug TO on;
```
######Making use of replication slots
```
max_replication_slots = 10
```
then
```
SELECT * FROM pg_create_physical_replication_slot('ke');
\d pg_replication_slots
```

delete
```
SELECT pg_drop_replication_slot('my_slot');
```
(tbc)
#####Chapter 3. Understanding Point-in-time Recovery
```
pg_basebackup
```
######Understanding the purpose of PITR
every time one of those segments is filled up and ready,postgres will call the ```archive_command```.  
transport XLOG file from database to an archive  
  
all xlog files must be present because postgres needs an uninterrupted sequence of xlog files.
######Archiving the transaction log
in postgresql.conf
```
wal_level = archive
archive_mode = on
archive_command = 'cp %p /archive/%f'
```
check
```
cd /var/lib/postgresql/9.5/main/pg_xlog/status
```
######Taking base backups
first in pg_hba,enable replication
```
host  replication  all     127.0.0.1/32      md5
```
then
```
pg_basebackup
```
update config. we dont need to restart postgres
```
select pg_reload_conf();
```
or
```
kill -hup 1234
```
we want to create a base backup that can live without XLOG? use
```
--xlog-method=stream
```
throttle
```
--max-rate
```
#####Chapter 14. Scaling with BDR
######Understanding BDR
evenual consistency  
if no new updates are made to a given data item, eventually all accesses to that item will return the last updated value.

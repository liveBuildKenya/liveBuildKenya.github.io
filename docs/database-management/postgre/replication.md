# Streaming Replication
This setup assumes you have already installed postgres in two machines; One master server and a standby server.

- Verify the cluster exists in both servers
```bash
    sudo pg_lsclusters
```

- To create a cluster run:
```bash
    sudo pg_createcluster <version> <name>
```

- Configure the master cluster, edit the file /etc/postgresql/{version}/{cluster}/postgresql.conf
```bash
    wal_level = replica
    wal_log_hints = on
    max_wal_senders = 10 
    wal_keep_segments = 64
```

- Configure the access to the access file /etc/postgresql/{version}/{cluster}/pg_hba.conf
```bash
    host replication <user> md5
```

- Create the replication user
```bash
    sudo -H -u postgres psql -c "CREATE USER <user> WITH replication;"
    sudo -H -u postgres psql -c "ALTER USER <user> WITH ENCRYPTED PASSWORD <password>;"
```

- Restart the main cluster
```bash
    sudo systemctl restart postgresql@10-main
```

- Configure Replica; edit the file /etc/postgresql/{version}/{cluster}/postgresql.conf
```bash
  wal_level = replica 
  wal_log_hints = on  
  max_wal_senders = 10 
  wal_keep_segments = 64 
  hot_standby = on 
```

- Edit host based access file
```bash 
    sudo nano /etc/postgresql/10/replica1/pg_hba.conf 
```

```bash
  local replication <user> md5 
```

- Setup standby replication; remove replica existing database files
```bash 
    sudo su - postgres 
    rm -rf /var/lib/postgresql/10/replica1 
```
- Sync replica with main cluster
```bash
    pg_basebackup -D /var/lib/postgresql/10/replica1 -U rep_user -w -P -R # -X stream
```
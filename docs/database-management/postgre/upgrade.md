# Upgrade PostgreSQL from 13 to 14
## pg_upgrade tool
- Check if there are any differences in the config files.

        diff /etc/postgresql/13/main/postgresql.conf /etc/postgresql/14/main/postgresql.conf
        diff /etc/postgresql/13/main/pg_hba.conf /etc/postgresql/14/main/pg_hba.conf

- Stop the PostgreSQL service.

        sudo systemctl stop postgresql@<version>-<cluster>

- Switch to postgres user.

        sudo su - postgres

- Check clusters (notice the --check argument, this will not change any data).

        /usr/lib/postgresql/14/bin/pg_upgrade \
        --old-datadir=/var/lib/postgresql/13/main \
        --new-datadir=/var/lib/postgresql/14/main \
        --old-bindir=/usr/lib/postgresql/13/bin \
        --new-bindir=/usr/lib/postgresql/14/bin \
        --old-options '-c config_file=/etc/postgresql/13/main/postgresql.conf' \
        --new-options '-c config_file=/etc/postgresql/14/main/postgresql.conf' \
        --check

- Migrate the data (without the --check argument).

        /usr/lib/postgresql/14/bin/pg_upgrade \
        --old-datadir=/var/lib/postgresql/13/main \
        --new-datadir=/var/lib/postgresql/14/main \
        --old-bindir=/usr/lib/postgresql/13/bin \
        --new-bindir=/usr/lib/postgresql/14/bin \
        --old-options '-c config_file=/etc/postgresql/13/main/postgresql.conf' \
        --new-options '-c config_file=/etc/postgresql/14/main/postgresql.conf'

- Run the recommended vacuumdb command:

        /usr/lib/postgresql/14/bin/vacuumdb --all --analyze-in-stages

There is one important change in the default settings in PostgreSQL 14. Actually, you may have noticed it while checking the differences in the config files: password encryption. Previously it was MD5, now it is SHA256. So, if you have used the default configuration, your clients will not be able to connect to the new database. And since PostgreSQL does not know the original clear text passwords, you have to set them again for all your database users.
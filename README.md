# Upgrading PostgreSQL Version (manually)

## Upgrading PostgresSQL to the latest version

1. Log in to the current server

   ```ssh forge@...```

2. Check if the latest version of Postgres already exists

      ```dpkg --get-selections | grep postgres```

      OR

      ```pg_lsclusters```

3. If it does not exist, install the latest version of Postgres on the server

    ```
    sudo apt-get update
    sudo apt-get -y install postgresql
    ```

4. Check the current cluster’s status and stop Postgres before you make any changes

    ```
    sudo pg_ctlcluster 14 main status
    sudo service postgresql stop
    ```

5. Rename the new Postgres version’s default cluster

    ```sudo pg_renamecluster 15 main main_pristine```

6. Upgrade the old cluster to the latest version

    ```
    sudo pg_upgradecluster 14 main --method=upgrade --link
    // If you use link mode, the upgrade will be much faster (no file copying) and
    // use less disk space, but you will not be able to access your old cluster once you start
    // the new cluster after the upgrade\
    ```

7. Make sure everything is working again

    ```sudo service postgresql start
    sudo pg_ctlcluster 15 main status
    pg_lsclusters
    ```

8. Drop old cluster

    ```sudo pg_dropcluster 14 main --stop
    // Drop the pristine database
    sudo pg_dropcluster 15 main_pristine --stop
    ```

9. (Optional) Another way to check the current DB version

     ```// Log in to postgres DB
     psql
     // select database
     \c babel
     // show current version
     SELECT version();
    // show databases
    \l
     // exit
     \q
     ```

10. Restart the server from Forge and re-deploy the master branch.

11. Useful commands

  a. How to remove  Postgresql by version
  
    sudo apt-get --purge remove postgresql-15

  b. How to stop a specific Postgres version

    pg_ctlcluster 15 main stop


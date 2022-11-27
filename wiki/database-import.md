1. Open an SSH-tunnel to the bastion host of the environment you want to use. Follow the instructions in the [onboarding document](onboarding.md) document. In this example, we'll use the dev environment, whose db is available on localhost port 10432.



2. Dump the databases you want to import into a cloud environment. In this example, we'll dump the dev environment itself to correct a migration problem in the environment. For other environments, adjust the database names and user names accordingly.

Another scenario for the procedure outlined here could be the import of jore3-importer data, which was created on your local machine. In this case, you should replace host and port to match the database in which the jore3 import was created.
```
pg_dump --data-only --no-owner --exclude-schema=hdb_catalog -F c -f dump.dump -h localhost -p 10432 -U dbadmindev jore4dev
pg_dump --data-only --no-owner --exclude-schema=hdb_catalog -F c -f ttdump.dump -h localhost -p 10432 -U dbadmindev timetablesdb
```



3. Generate SQL from the dumps. In case you are creating a dump from a cloud database, it is a good idea to do this in a separate step to save network bandwidth.

NB: The "--cluster" parameter determines the version of pg_restore to be used. It may not be required in all circumstances or you may have to adjust the value provided ("14/main").

```
pg_restore -F c --disable-triggers --no-owner --role=dbhasuradev -f dump.sql --cluster 14/main dump.dump
pg_restore -F c --disable-triggers --no-owner --role=dbhasuradev -f ttdump.sql --cluster 14/main ttdump.dump
```



4. Insert an SQL statement to disable triggers during the import of the dumps in the beginning of each created SQL file. Make sure the tool you use for this can handle large text files.

This step is needed, because the managed postgresql instance of the cloud environment does not provide "true admin" access. Therefore the "--disable-triggers" switch used in above "pg_restore" command cannot disable all triggers.

```
SET session_replication_role = replica;
```



5. If needed, deploy the correct version of hasura in the cloud environment, into which the dump should be imported later. The correct version is that one, whose migrations match the state of the dumps you want to import.

For this you can simply create a matching flux configuration, which can be pushed to the appropriate branch of the jore4-flux repository.

It is not important if this version of hasura will start correctly at this stage, since we are about to kill all hasura instances anyway. Deploying the correct version now will only ensure the right version will be started at a later stage. 



6. Uninstall flux from the kubernetes cluster of the cloud environment, into which you are about to import the dump. This is needed to keep hasura and the jore3-importer from being restarted immediately, because we will need to shut them down for some time.

For example, to uninstall flux from the dev environment, run the following lines in the flux repository directory:

```
./kubernetes.sh login dev
flux uninstall --namespace=flux-system
```



7. Scale the amount of hasura and jore3-importer instances to 0 in the cloud environment into which the dump should be imported. We need to do this in order to be able to re-create the database(s) for the import (cannot drop databases which have clients connected).

In case you use a VPN to access the kubernetes cluster, make sure the connections to the cluster are actually routed through the VPN. If needed, add a route manually, e.g.:

```
sudo route add -host hsl-jore4-dev-cluster-dns-e2451442.hcp.westeurope.azmk8s.io dev tun0
```

Then use a tool of your choice (kubectl or Lens ui) to scale the deployments of importer and hasura both to 0 instances.



8. Connect to the database server to re-create the logical databases, for example use:

```
psql -h localhost -p 10432 -U dbadmindev postgres
```

Then re-create the databases into which to import the dumps. Make sure to use the same statements, which are used during cloud environment deployment, so as to create the correct extensions and grant the correct privileges (check the appropriate steps in the jore4-deploy repository):

```
DROP DATABASE jore4dev;
CREATE DATABASE jore4dev;
\c jore4dev
CREATE EXTENSION IF NOT EXISTS pgcrypto;
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS btree_gist;
GRANT CREATE ON DATABASE jore4dev TO dbhasuradev;
DROP DATABASE timetablesdb;
CREATE DATABASE timetablesdb;
GRANT CREATE ON DATABASE timetablesdb TO dbhasuradev;
\c timetablesdb
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```



9. Scale the amount of hasura instances in the cloud environment to 1. This will start a single hasura instance to run all migrations needed to import the data (see step 5).

Wait and check that the instance starts correctly and is healthy. This means all migrations have been run correctly.

Next, scale the amount of hasura instances back to 0, effectively killing the instance created previously. Now the cloud environment's database contains the freshly created databases with the correct migrations run.



10. Import the database dumps into the cloud environment's databases (finally!)

E.g. to import dumps into the dev environments databases, which are available on local host port 10432:

```
psql -h localhost -p 10432 -U dbadmindev -d jore4dev < dump.sql
psql -h localhost -p 10432 -U dbadmindev -d timetablesdb < ttdump.sql
```



11. Re-install flux on the cloud environment's kubernetes cluster. This will restart the hasura and jore3-importer instances.

Run the following commands in the jore4-flux repository's directory:

```
flux install --namespace=flux-system
```



12. In case the dumps were created by the jore3-importer and used older database schemas, you may now deploy a new version of hasura in the cloud environment in order to migrate the imported data to a newer schema.

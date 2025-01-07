# UPGRADING POSTGRES 11.21-bullseye DATABASE TO 12.16-bullseye USING DOCKER

This folder creates a postgresql image based on the 11.21-bullseye and 12.16-bullseye, to upgrade postgres database from version 11.21-bullseye to 12.16-bullseye.

## Build and development

### Creating a 11.21-bullseye container and populating the database(made for laboratory only):

**Run and add data to the table**

Run docker-compose using the file in the 11.21-bullseye directory.

```bash
docker-compose -f ./11.21-bullseye/docker-compose.yml up -d
```

Access the container and add data to the table.

```bash
docker exec -it 11.21-bullseye psql -U postgres -d teste

CREATE TABLE teste (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255)
);

INSERT INTO teste (nome) VALUES
('Alice'),
('Bob'),
('Charlie'),
('David');

SELECT * FROM teste;
```

Stop the 11.21-bullseye container which will already have the data.

```bash
docker exec -it -u postgres 11.21-bullseye /bin/bash

/usr/lib/postgresql/11/bin/pg_ctl -D /var/lib/postgresql/data stop
```

### To build and development the image locally:

**Build and Run**

Before running docker-compose, make a backup of the previously mentioned version 11 of the database directory.

If you have completed the step of creating and populating the table, there is no need to do anything. If you have an existing database and want to upgrade it, the data directory must be copied to the path ./dados_postgres/11 .

Build and Deploy the docker image on your machine using the command below.

```bash
docker build -t postgres:11to12 11to12/

docker-compose -f ./11to12/docker-compose.yml up -d
```

### Upgrade procedure

At this point we will need to create a new database in version 12 of Postgres but we are not going to upload the database yet, we will start the database and then carry out the procedure according to the commands below.

Enter in the container postgres11to12 and go to the /tmp path to start the procedure.

```
docker exec -it -u root postgres11to12 /bin/bash

chown postgres:postgres /var/lib/postgresql/data

exit

docker exec -it postgres11to12 /bin/bash

cd /tmp

/usr/lib/postgresql/12/bin/initdb
```

That's the log when the initbd succeed:

```
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    /usr/lib/postgresql/12/bin/pg_ctl -D /var/lib/postgresql/data -l logfile start

```

To check:

```
/usr/lib/postgresql/12/bin/pg_upgrade --old-bindir /usr/lib/postgresql/11/bin/ --new-bindir /usr/lib/postgresql/12/bin/ --old-datadir /var/lib/postgresql/11/ --new-datadir /var/lib/postgresql/data/ --check
```

If you receive the message **Clusters are compatible** you can run the command without the --check flag.

```
/usr/lib/postgresql/12/bin/pg_upgrade --old-bindir /usr/lib/postgresql/11/bin/ --new-bindir /usr/lib/postgresql/12/bin/ --old-datadir /var/lib/postgresql/11/ --new-datadir /var/lib/postgresql/data/
```

This process can take minutes to hours depending on the size of the bank you are upgrading from. Once the upgrade is successful. Give the command `postgres` to start the database.

To make sure that the database has been completely updated, in the second terminal use the /tmp/analyze_new_cluster.sh script, it will do a scan to check if everything is ok.

Quit the container and stop it, after stop start the postgres13 container and test the data.

```bash
exit
docker-compose -f ./11to12/docker-compose.yml down
docker-compose -f ./12.16-bullseye/docker-compose.yml up -d
```

Access the container and add data to the table.

```bash
docker exec -it 12.16-bullseye psql -U postgres -d teste

SELECT * FROM teste;
```

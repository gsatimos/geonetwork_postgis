
Geonetwork with Postgis database

### Run Postgis with docker-compose 
Contains
- postgis container
- a bridge network on 172.18.0.0/16 subnet

Database is persisted on host at `/docker-data/postgis` so this directory must be created first i.e. `mkdir -p /docker-data/postgis`

```
docker-compose up -d 
```

Notes:
- The postgis container's pg_hba.conf files only allows ip connections from a given set of CIDRs (one of which is 172.18.0.0/16). This is why a subnet with this CIDR is created and then used by the geonetwork container.
- Postgis must be running when the geonetwork container is started.
- The postgis database can be truncated anytime by removing the host-persisted data `rm -rf /docker-data/postgis` and restarting the docker-compose stack and the geonetwork container.

### Run up geonetwork container 

```
docker run \
  --name geonetwork \
  -p 8080:8080 \
  --network geonetworkpostgis_net \
  -e POSTGRES_DB_HOST=postgis \
  -e POSTGRES_DB_PORT=5432 \
  -e POSTGRES_DB_USERNAME=docker \
  -e POSTGRES_DB_PASSWORD=docker \
  geonetwork:postgres
```

Notes:
- Username/Password: `admin/admin`


### Browse geonetwork database

```
docker exec -i -t geonetworkpostgis_postgis_1 /bin/bash
psql -h localhost -U docker gis #<-- password 'docker'

gis$ \c geonetwork
geonetwork$ \d+

```

### Documentation of containers

Geonetwork container:
https://hub.docker.com/_/geonetwork/

Postgis container:
https://github.com/kartoza/docker-postgis


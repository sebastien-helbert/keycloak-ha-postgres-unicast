# Keycloak HA PostgreSQL without multicast

Extension of jboss/keycloak-ha-postgres official image which don't rely on UDP (multicast) for clustering but use TCP (unicast) instead.

## Supported tags and respective `Dockerfile` links

-	[`3.4.1.Final`, `latest` (*3.4.1.Final/Dockerfile*)](https://github.com/sebastien-helbert/keycloak-ha-postgres-unicast/blob/3.4.1.Final/Dockerfile)

## How to use this image

### Start a PostgreSQL instance

First start a PostgreSQL instance using the PostgreSQL docker image:

    docker run --name postgres -e POSTGRES_DATABASE=keycloak -e POSTGRES_USER=keycloak -e POSTGRES_PASSWORD=keycloak -e POSTGRES_ROOT_PASSWORD=password -d postgres

### Start a Keycloak HA instance

Start one Keycloak instance that form a cluster and connect to the PostgreSQL instance running in previously started 'postgres' container:

    docker run --name keycloak1 -p 8081:8080 --link postgres:postgres -e POSTGRES_DATABASE=keycloak -e POSTGRES_USER=keycloak -e POSTGRES_PASSWORD=keycloak shelbert/keycloak-ha-postgres-no-udp


Browse to [http://localhost:8081](http://localhost:8081]) and connect to the first instance.

Start more Keycloak instances that form a cluster with the first one :

    docker run --name keycloak2 -p 8082:8080 --link keycloak1:keycloak1 --link postgres:postgres -e POSTGRES_DATABASE=keycloak -e POSTGRES_USER=keycloak -e POSTGRES_PASSWORD=keycloak -e JGROUPS_TCPPING_INITIAL_HOSTS=keycloak1[7600] shelbert/keycloak-ha-postgres-no-udp


Browse to [http://localhost:8082](http://localhost:8082) and check that modifications in first instance are visible from the second one and vice versa.


## Environment Variables

### `JGROUPS_TCPPING_INITIAL_HOSTS`

Optional. Cluster nodes in the form `<host1>[7600],<host1>[7600],<host3>[7600]` for example : `keycloak1[7600],keycloak2[7600],keycloak3[7600]`

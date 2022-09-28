# goobi-viewer-docker

Goobi viewer Docker related files

## Prerequisites

- [docker](https://docs.docker.com/get-docker/)
- [docker-compose](https://docs.docker.com/compose/install)
- this repository

```bash
sudo apt install docker.io docker-compose mariadb-client python3-passlib python3-bcrypt
git clone https://github.com/intranda/goobi-viewer-docker.git
```

## First time installation

- change database credentials in the docker-compose file
- adjust VIEWER_DOMAIN environment variable in docker-compose-file

```bash
sudo chown 8983:8983 ./data/solr
docker compose up -d viewer-db solr
docker compose exec solr solr zk upconfig -n goobiviewer -d /opt/goobiviewer
docker compose exec solr solr create -c collection1 -n goobiviewer
docker compose up -d
```

## Configuration

### Changed values for docker setup

- The solr index is not reachable on localhost, but via the hostname "solr" when using the example setup. The solr URL must thus be configured accordingly when using custom configuration.
- The viewer is not reachable for other services (like the indexer) on localhost but via the hostname "viewer". The viewer URL must be configured accordingly when using custom configuration.

### Bind mount local folder with configuration

The most straight forward way to include larger sets of configuration is to mount it into the continer (see docker-compose.yml). This is also the preferred way when migrating to a docker bases Goobi viewer setup.

### Copy configuration files into a container at startup

To copy a folder of config files into the viewer container during startup, you can do the following:

- Provide a set of configfiles (e.g. in  `data/viewer/config`)
- modify the viewer service in the docker-compose file as follows by adding environment settings used in the startup script:

    ```yaml
    environment:
      CONFIGSOURCE: folder
      CONFIG_FOLDER: "/config"
    ```

- also bind mount the local folder into the container by adding to the volume section of the viewer service:

    ```yaml
    - type: bind
      source: ./data/viewer/config/
      target: /config
   ```

## Database Access

The provided example docker-compose.yml does allow access to the database server via localhost:3306 with the credentials provided in the file as well. For production use these credentials should be changed and the port forwarding should possible be deactivated.

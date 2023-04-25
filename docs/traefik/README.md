Copy ```traefik.yml``` file to ```/etc/traefik/``` before deploying of this docker container.

```
mkdir /etc/traefik/
cp traefik.yml /etc/traefik/
```

Create docker networks

```
docker network create proxy
docker network create chirpstack-internal
```

Start Traefik docker containter.

```
docker-compose up -d
```


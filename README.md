
# Webdav Server based on debian-testing

## Cloning the repository

> git clone https://github.com/reply2future/docker-webdav.git

and we access the inside of the folder :

cd docker-webdav

## Building the image
docker build -t reply2future/webdav:arm .

## Check the image number:
docker images

## Mounting the container

### docker-cli
USERNAME: webdav
PASSWORD: webdav
PORT: 80

--restart=unless-stopped: Start each time we start the server

```bash
docker run --name webdav \
  --restart=unless-stopped \
  -p 80:80 \
  -v $HOME/docker/webdav:/media \
  -e USERNAME=webdav \
  -e PASSWORD=webdav \
  -e TZ=Europe/Madrid \
  -e UID=1000 \
  -e GID=1000 \
  -d  uge ek/webdab:arm
```

### docker-compose with traefik and reverse proxy

```yaml
version: '2'
services:
  webdav:
    container_name: webdav
    image: ugeek/webdav:arm
    ports:
      - 80:80
    volumes:
      - $HOME/docker/webdav:/media
    environment:
      - USERNAME=webdav
      - PASSWORD=webdav
      - UID=1000
      - GID=1000
      - TZ=Europe/Madrid
    networks:
      - web
    labels:
      - traefik.backend=webdav
      - traefik.frontend.rule=Host:webdav.tu_dominio.duckdns.org
      - traefik.docker.network=web
      - traefik.port=80
      - traefik.enable=true
      # Adding in secure headers
      - traefik.http.middlewares.securedheaders.headers.forcestsheader=true
      - traefik.http.middlewares.securedheaders. headers.sslRedirect=true
      - traefik.http.middlewares.securedheaders.headers.STSPreload=true
      - traefik.http.middlewares.securedheaders.headers.ContentTypeNosniff=true
      - traefik.http.middlewares.securedheaders.headers.BrowserXssFilter=true
      - traefik.http.middlewares.securedheaders.headers.STSIncludeSubdomains=true
      - traefik.http.middlewares.securedheaders.headers.stsSeconds=63072000
      - traefik.http.middlewares.securedheaders.headers.frameDeny=true
      - traefik.http.middlewares.securedheaders.headers.browserXssFilter=true
      - traefik.http.middlewares.securedheaders.headers.contentTypeNosniff=true
networks:
  web:
   external: true
```

Enter the command...
> docker-compose up -d


## Logs

New log record added.

### See logs

> docker exec -it webdav cat /var/log/nginx/webdav_access.log

### Real Time Logs

> docker exec -it webdav cat /var/log/nginx/webdav_access.log

### Error Logs

> docker exec -it webdav /var/log/nginx/webdav_error.log

## Acknowledgements
- Thanks to [Germán Martín](https://github.com/gmag11) for adding compatibility with Windows 10 clients. [Fork](https://github.com/gmag11/docker-webdav)
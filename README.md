# **Containered Micro-Services**
**Docker containers configurations**

This is a reposity to my personal use container's and configuration files, to expose my personal project of deploying and configurating a personal ubuntu server with containered micro services with free licenses, these services can be potentially escalated to a enterprise use.

All services are deployed with free licenses without cost's

## **Project**

1. Ubuntu Server LTS instalattion and configuration
2. Basic linux management
3. Installation of docker
4. Network management

## **Services and Integrations:**

1. Portainer
2. Wake on Lan
3. Private VPN - Wireguard
4. Free DNS Server solution - Pi hole
5. Free web page to redirect my ip's easily - Heimdall
6. My own cloud service - Nextcloud
8. Onlyoffice services integrated with Nextcloud
9. Reverse proxy for management of public internet acess and management of SSL - Nginx Proxy Manager
10. Auto duckdns external ip and ipv6 uptade of domains - Duckdns
11. Backup service with disaster control - Duplicati

## **Directory Structure**

- *GithubDocker*  // Where we going to store all volumes

   - *containers-folders* // Here we going to create one folder to integrated micro-services
   
      - *app data config* // Some containers have dependencies, like databases, internal-proxies, data storage and here we going to divide that to facilitate manutention of configs. Here i'm going to store the docker-compose.yml o each service that can be used to deploy without the portainer management stack.

**Keep in mind, rename this folders need's changes in all docker-composes.yml files to indicate the new correct path**

## **Docker-compose Services**

The complete instructions of configuration can be found in the README.md of each service folder

[Portainer](/containers/portainer)

I *Strongly* recommend the use of the portainer to deploy the services, because is easialy to visualize the informations, restarting services, modifying resources, checking logs but ins't mandatory. You can use the docker-compose.yml pre-mades of this repository for each service to configure and deploy.

```yaml
---
version: "3"
services:
   app:
     image: 'portainer/portainer-ce:latest'
     restart: unless-stopped
     container_name: portainergit
     ports:
        - 8000:8000
        - 9443:9443
     volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - /GithubDocker/containers/portainer:/data portainer
```

[Heimdall](/containers/heimdall)

WebUI to organizate and configure all your IP's and port's, is a easy way to access your's services without typing the URL and port's all the time.

```yaml
---
version: "3.2"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdallgit
    environment:
      - PUID=1000
      - PGID=1000
      - TZ="America/Sao_Paulo"
    volumes:
      - /GithubDocker/containers/heimdall:/config
    ports:
      - 8088:80
      - 4443:443
    network_mode: bridge
    restart: unless-stopped
```

[Pi-hole](/containers/pihole)

Personal DNS server for internal LAN.

```yaml
---
version: "3"
services:
  pihole:
    container_name: piholegit
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "82:80/tcp"
    environment:
      TZ: 'America/Sao_Paulo'
      WEBPASSWORD:
      FTLCONF_LOCAL_IPV4:
      VIRTUAL_HOST:
      VIRTUAL_PORT: 82
      WEB_PORT: 82

    volumes:
      - '/GithubDocker/containers/pihole/app:/etc/pihole'
      - '/GithubDocker/containers/pihole/dnsmasq:/etc/dnsmasq.d'
    network_mode: host
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

[Duckdns](/containers/duckdns)

Free DDNS domain services, i use them because they attend for me, but you can use normally your favorite DDNS without problems.

```yaml
---
version: "3"
services:
  duckdns:
    image: lscr.io/linuxserver/duckdns:latest
    container_name: duckdns
    environment:
      - PUID=1000 #optional
      - PGID=1000 #optional
      - TZ=America/Sao_Paulo
      - SUBDOMAINS=
      - TOKEN=
    volumes:
      - /GithubDocker/containers/duckdns:/config #optional
    network_mode: bridge
    restart: unless-stopped
```

[Wireguard](/containers/wireguard)

My personal access VPN, i'm going to use nextcloud, office and heimdall with reverse proxy, but i want to remain some services only internal. Them i use my VPN to access them.

```yaml
---
version: "3"
services:
  wireguard:
    image: lscr.io/linuxserver/wireguard:latest
    container_name: wireguardgit
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Sao_Paulo
      - SERVERURL=
      - SERVERPORT=51820
      - PEERS=
      - PEERDNS=
      - INTERNAL_SUBNET=10.13.13.0
      - ALLOWEDIPS=0.0.0.0/0
      - PERSISTENTKEEPALIVE_PEERS=all
      - LOG_CONFS=true
    volumes:
      - /GithubDocker/containers/wireguard:/config
      - /GithubDocker/containers/wireguard/lib/modules:/lib/modules #optional
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    network_mode: bridge
    restart: unless-stopped
```

[Nginx Proxy Manager](/containers/proxy)

Version of nginx with a UI, its easy to configure our proxy and manage our let'sencrypt SSL certs.

```yaml
---
version: "3"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '443:443'
      - '81:81'
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm"
      DB_MYSQL_NAME: "npm"
    volumes:
      - /GithubDocker/containers/proxy/data:/data
      - /GithubDocker/containers/proxy/letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 'npm'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm'
    volumes:
      - /GithubDocker/containers/proxy/mysql:/var/lib/mysql

networks:
   bridge:
     driver: bridge
     external: true
```

[Nextcloud](/containers/nextcloud)

Private Cloud.

```yaml
version: "3"

volumes:
  nextcloud:
  db:

services:
  db:
    image: mariadb:10.5
    container_name: nextcloud-db
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - /GithubDocker/containers/nextcloud/db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=nextcloud
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
    network_mode: bridge

  app:
    image: nextcloud:latest
    container_name: nextcloud-app
    restart: always
    ports:
      - 8080:80
    links:
      - db
    volumes:
      - /GithubDocker/containers/nextcloud/app:/var/www/html
    storage_opt:
      size: '300G'
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
    network_mode: bridge
```

Relative Path of arquive config.php: *./nextcloud/app/config/config.php*

Trust domain config
```php
'trusted_domains' =>
  array (
    0 => 'example1.duckdns.org',
    1 => 'example2.duckdns.org',
  ),
```

Trust proxy Config.
```php
'trusted_proxies' =>
  array (
    0 => '172.0.0.11',
  ),
```

```php
  'overwritehost' => 'example1.duckdns.org',
  'overwriteprotocol' => 'https',
```

*webcarddav* and *webcaldav*:
```php
rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json last;

location = /.well-known/carddav {
   return 301 $scheme://$host:$server_port/remote.php/dav;
 }
location = /.well-known/caldav {
   return 301 $scheme://$host:$server_port/remote.php/dav;
}
```

[Onlyoffice](/containers/onlyoffice)

Office service integrated with Nextcloud to document editing.

```yaml
---
version: "3"
volumes:
  onlyoffice:

services:
  onlyoffice:
     image: onlyoffice/documentserver
     container_name: onlyoffice
     restart: unless-stopped
     ports:
      - 8082:80
     environment:
      - VIRTUAL_PORT=8082
      - VIRTUAL_HOST=
      - WOPI_ENABLED=true
      #Uncomment following lines to activate JWT
      #- JWT_ENABLED=true
      #- JWT_SECRET=
      #- JWT_HEADER=Authorization
      #- JWT_IN_BODY=true
      #- ONLYOFFICE_HTTPS_HSTS_ENABLED=false  
     volumes:
      - /GithubDocker/containers/onlyoffice/logs:/var/log/onlyoffice
      - /GithubDocker/containers/onlyoffice/data:/var/www/onlyoffice/Data
      - /GithubDocker/containers/onlyoffice/lib:/var/lib/onlyoffice
      - /GithubDocker/containers/onlyoffice/rabbitmq:/var/lib/rabbitmq
      - /GithubDocker/containers/onlyoffice/redis:/var/lib/redis
      - /GithubDocker/containers/onlyoffice/db:/var/lib/postgresql
      - /GithubDocker/containers/onlyoffice/usr:/usr/share/fonts/truetype/custom
     network_mode: bridge
```

[Wake on Lan](/containers/wol)

Wake on lan webUI.

```yaml
---
version: '3.9'
services:
  backend:
    container_name: wol-server
    image: golang:1.17.6-alpine3.15
    restart: unless-stopped
    volumes:
      - .:/wol-web
      - ./frontend:/frontend
    entrypoint: sh -c
    command:
      - |
        cd /wol-web/backend
        apk add build-base
        go build -o server .
        /wol-web/backend/server
    network_mode: 'host'
volumes:
  wol-web-db:
```

[Duplicati](/containers/duplicati)

Backup service.

```yaml
---
version: "2.1"
services:
  duplicati:
    image: lscr.io/linuxserver/duplicati:latest
    container_name: duplicati
    environment:
      - PUID=0 #there is some services with folders acessible only by root
      - PGID=0 #there is some services with folders acessible only by root
      - TZ=Brazil
    volumes:
      - /GithubDocker/containers/duplicati/config:/config
      - /duplicati_backups:/backups # create a folder outside the main folder "GithubDocker", that folder is where goes the backups
      - /GithubDocker/containers:/source # folder the duplicati can see to do the backups
    ports:
      - 8200:8200
    network_mode: bridge
    restart: unless-stopped
```

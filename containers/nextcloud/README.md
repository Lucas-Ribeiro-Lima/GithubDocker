# NEXTCLOUD

[Nextcloud Official Site](https://nextcloud.com/)

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

Some extra configurations will be necessary if Nextcloud is used with a FQDN and with a proxie.

That is responsible to say what domains is secure to Nextcloud.
Relative Path of arquive: *./nextcloud/app/config/config.php*
```php
'trusted_domains' =>
  array (
    0 => 'example1.duckdns.org',
    1 => 'example2.duckdns.org',
  ),
```
Trust configuration for proxie.
```php
  'trusted_proxies' =>
  array (
    0 => '172.0.0.11',
  ),
```
That configuration is responsible to reject request behind the proxy and redirect you to the correct address. If someone try to reach Nextcloud by the IP address, the own Nextcloud redirect you to the front of proxie utilizing the corret protocol 'https'. That is a best way to secure all connections.
```php
  'overwritehost' => 'example1.duckdns.org',
  'overwriteprotocol' => 'https',
```

We will need extra *webcarddav* and *webcaldav* configurations for the Nextcloud proxie to work correctly.

Add these lines in the advanced custom NGINX configuration:

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
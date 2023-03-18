# NEXTCLOUD

[Officel Site](https://nextcloud.com/)

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
Relative Path of arquive: *./nextcloud/app/config/config.php*
```php
'trusted_domains' =>
  array (
    0 => 'example1.duckdns.org',
    1 => 'example2.duckdns.org',
  ),
```

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
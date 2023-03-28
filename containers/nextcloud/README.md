# NEXTCLOUD

[Nextcloud Official Site](https://nextcloud.com/)

## Docker-Compose

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
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
    network_mode: bridge
```

## Reverse Proxy Configuration

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
## PHP Memory Limit

To increase PHP memory limit you can add the follown environment variable to docker-compose

```yml
- PHP_MEMORY_LIMIT=1028M
```
To verify the actual PHP memory limit you can use the following command changing the container name *nextcloud-app* to yours.
```shell
sudo docker exec nextcloud-app /bin/bash -c 'env' | grep PHP_MEMORY_LIMIT
```

## CronJobs

The default background process is AJAX, but nextcloud suggest we use cronjobs to do the work. To do that i opted to use cronjob on host executing docker exec commands.

```shell
crontab -e

*/5 * * * * docker exec -u www-data nextcloud-app php -f /var/www/html/cron.php

*/15 * * * * docker exec -u www-data nextcloud-app php -f /var/www/html/occ preview:pre-generate -vvv
```

## Preview Generator APP

We need install some packages, ffmpeg, imagemagick and ghostscript. To customize the official image use the following command on images portainer.

```shell
FROM nextcloud:25

RUN apt-get update && apt-get install -y imagemagick ffmpeg ghostscript && apt -y install smbclient libsmbclient-dev && pecl install smbclient && docker-php-ext-enable smbclient && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2-foreground"]
```

To enable preview to video files you will need install the preview generator APP of the Nextcloud store them configure the config.php

```php
    'preview_max_x' => '2048',
    'preview_max_y' => '2048',
    'jpeg_quality' => '60',
    'ffmpeg' => '/usr/bin/ffmpeg',
    'enable_previews' => true,
    'enabledPreviewProviders' =>
    array (
        0 => 'OC\\Preview\\TXT',
        1 => 'OC\\Preview\\MarkDown',
        2 => 'OC\\Preview\\OpenDocument',
        3 => 'OC\\Preview\\PDF',
        4 => 'OC\\Preview\\MSOffice2003',
        5 => 'OC\\Preview\\MSOfficeDoc',
        6 => 'OC\\Preview\\Image',
        7 => 'OC\\Preview\\Photoshop',
        8 => 'OC\\Preview\\TIFF',
        9 => 'OC\\Preview\\SVG',
        10 => 'OC\\Preview\\Font',
        11 => 'OC\\Preview\\MP3',
        12 => 'OC\\Preview\\Movie',
        13 => 'OC\\Preview\\MKV',
        14 => 'OC\\Preview\\MP4',
        15 => 'OC\\Preview\\AVI',
    ),
```
Then we going to executing the script to generate all previews.

```shell
docker exec -u www-data nextcloud-app php -f /var/www/html/occ preview:generate-all -vvv
```

After that we only need the cronjob pre-generating previews. Just return to Cron configuration if you skipped that.
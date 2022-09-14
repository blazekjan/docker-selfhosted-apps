# Nextcloud 

Nextcloud is an open source suite of client-server software for creating and using file hosting services with wide cross platform support.

The Nextcloud server is written in PHP and JavaScript. For remote access it employs sabre/dav, an open-source WebDAV server. It is designed to work with several database management systems, including SQLite, MariaDB, MySQL, PostgreSQL.

There are many ways to deploy Nextcloud. This setup consists of:

 - nextcloud-db (PostgreSQL)
 - nextcloud-cache (Redis)
 - nextcloud-app (php-fpm container without webserver)
 - nextcloud-cron

We will serve the whole Nextcloud container just through caddy-docker-proxy labels. So it should perform a little bit better.
 
Using  [PHP-FPM](https://www.cloudways.com/blog/php-fpm-on-cloud/)  for better performance and using  [Redis](https://aws.amazon.com/redis/)  for more reliable  [transactional file locking](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/files_locking_transactional.html)  and for  [memory file caching](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/caching_configuration.html).

## Docker compose 

**WARNING!**  I am using latest  **Compose v2**  (currently version  **2.11.0**) and  **latest docker compose reference**, which is a  **major overhaul!**   
Be careful when following my instructions! In case of trouble you should visit this site:  [Compose specification | Docker Documentation](https://docs.docker.com/compose/compose-file/)  and look what is new and / or changed.

Copy  **compose.yaml** config, edit it where needed and bring it up with:

    $ docker compose up -d


Caddy needs to see the Nextcloud volume to make it work, so don't forget to add following volume to caddy-docker-proxy compose.yaml file:

    - $DOCKERDIR/nextcloud/nextcloud_data/:/nextcloud/var/www/html
    
Create corresponding .env file or replace the variable with the absolute path.

**WARNING!** Don't let the caddy-docker-proxy create the volume, as there will be messed ownership rights and Netxcloud **WILL NOT** install. Be sure to bring up the Nextcloud stack first.

## Post-install configuration
Nextcloud will most likely throw few warnings, which should be quite easy to resolve:

*"The reverse proxy header configuration is incorrect, or you are accessing Nextcloud from a trusted proxy. If not, this is a security issue and can allow an attacker to spoof their IP address as visible to the Nextcloud. Further information can be found in the documentation."*

Edit the **config.php** located here:
```
~/docker/nextcloud/nextcloud_data/config
```
Insert this:
```
'trusted_proxies' =>
   array (
      0 => gethostbyname('caddy-docker-proxy_container_name'),
   ),
```

*Your installation has no default phone region set. This is required to validate phone numbers in the profile settings without a country code. To allow numbers without a country code, please add "default_phone_region" with the respective ISO 3166-1 code of the region to your config file.*

Please refer to [List of ISO 3166 country codes - Wikipedia](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) and choose the correct Alpha-2 code that matches your country. Insert it to **config.php**.
```
'default_phone_region' => 'CZ',
```
*Module php-imagick in this instance has no SVG support*

This should be safely ignored, as this is highly debatable if it is worth fixing. It might pose a security threat and SVG is disabled in Nextcloud by default anyways.

Source: [SVG imagemagick Nextcloud 21.0 Docker · Issue #1414 · nextcloud/docker (github.com)](https://github.com/nextcloud/docker/issues/1414)

If you did everything right, the Nextcloud should be running safe and sound.

**TO DO:**
 - Polish this README
 - Write more text
 - Make more fine tuning in the compose file (docker secrets, .env variables)

# docker-nginx-owncloud
Owncloud in Docker container based on phusion baseimage

## Why this image
There are already some docker images for owncloud, that one is focused on working on phusion baseimage and using the ubuntu ppa for owncloud. 
Installation is free and based on the occ client interface

## Production Ready

It's always nice to have a proper reverse proxy and https is must have nowadays, so we use this image within the [nginx-proxy](https://github.com/jwilder/nginx-proxy), and the [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)

Once both nginx container are setup you can build it and launch it.

    docker build -t "houseofagile/docker-nginx-owncloud:latest" .

    docker run -e VIRTUAL_HOST="someserver.example.com" -e LETSENCRYPT_HOST="someserver.example.com" -e LETSENCRYPT_EMAIL="jc@houseofagile.com" -d --name someserver-owncloud -h someserver-owncloud -P houseofagile/docker-nginx-owncloud:latest
    
## Post Install
### Install owncloud

For personal use, sqlite should be fine, but of course you can choose any of the provided database driver, you will just have to customize the install command line.

Connect to your docker owncloud container and install owncloud

    docker exec -it someserver-owncloud bash
    
    root@someserver-owncloud:/# cd /var/www/owncloud/
    
    root@someserver-owncloud:/# sudo -u www-data php occ maintenance:install
    

### Add your domain name to the trusted server config

    sed -i "/.*0 => 'localhost',/a \\    1 => 'someserver.example.com'," /var/www/owncloud/config/config.php


# docker-nginx-owncloud
Owncloud in Docker container based on phusion baseimage

## Why this image
There are already some docker images for owncloud, that one is focused on working on phusion baseimage and using the ubuntu ppa for owncloud. 
Installation is free and based on the occ client interface

## Production Ready

It's always nice to have a proper reverse proxy and https is must have nowadays, so we use this image within the [nginx-proxy](https://github.com/jwilder/nginx-proxy), and the [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)

## Post Install
### Install owncloud

For personal use, sqlite should be fine, but of course you can choose any of the provided database driver, you will just have to customize the install command line.

### Add your domain name to the trusted server config
    sed -i "/.*0 => 'localhost',/a \\    1 => 'someserver.example.com'," /var/www/owncloud/config/config.php


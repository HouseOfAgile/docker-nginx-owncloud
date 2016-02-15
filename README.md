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

## Caveats

As this owncloud will be behind the jwilder proxy, you might need to change the max upload size for files, as you want to have huge files on your owncloud =] By default, the jwilder proxy container is limited, but then [explain you how to change it](https://github.com/jwilder/nginx-proxy#proxy-wide)

As an example here is how we run it with a 512M limit:

    docker run -d -p 80:80 -p 443:443 --name nginx-proxy -v /var/local/nginx/certs:/etc/nginx/certs:ro -v /home/hoauser/work/nginx-conf/fat_body_size.conf:/etc/nginx/conf.d/fat_body_size.conf -v /etc/nginx/vhost.d -v /usr/share/nginx/html -v /var/run/docker.sock:/tmp/docker.sock:ro jwilder/nginx-proxy
    hoauser@server:~/work/nginx-conf$ cat /home/hoauser/work/nginx-conf/fat_body_size.conf
    client_max_body_size 512m;



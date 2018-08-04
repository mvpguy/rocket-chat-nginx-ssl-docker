# rocket-chat-nginx-ssl-docker
A step-by-step guid to installing Rocket.chat with Nginx and an SSL certificate

Here is what worked for me:

I started with a Digital Ocean container with a one-click install of Docker pre-installed.

1. Installing nginx + SSL (from Let’s encrypt free SSL certificate)
This step was based on this repo for a getting set up with Nginx with auto SSL from Let’s Encrypt:

$ mkdir nginx
$ cd nginx
$ mkdir data 
$ mkdir ssl
$ cd ssl
$ git clone https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git
$ cd docker-compose-letsencrypt-nginx-proxy-companion
#create and modify .env file to add IP and networname
$ touch .env
$ sudo nano .env
Copy file from example.env and modify your specific details (IP)

#
# docker-compose-letsencrypt-nginx-proxy-companion
# 
# A Web Proxy using docker with NGINX and Let's Encrypt
# Using the great community docker-gen, nginx-proxy and docker-letsencrypt-nginx-proxy-companion
#
# This is the .env file to set up your webproxy enviornment

#
# Your local containers NAME
#
NGINX_WEB=nginx-web
DOCKER_GEN=nginx-gen
LETS_ENCRYPT=nginx-letsencrypt

#
# Your external IP address
#
IP=0.0.0.0

#
# Default Network
#
NETWORK=webproxy

#
# Service Network (Optional)
#
# In case you decide to add a new network to your services containers you can set this
# network as a SERVICE_NETWORK
#
# [WARNING] This setting was built to use our `start.sh` script or in that special case
#           you could use the docker-composer with our multiple network option, as of:
#           `docker-compose -f docker-compose-multiple-networks.yml up -d`
#
#SERVICE_NETWORK=webservices

#
# NGINX file path
#
NGINX_FILES_PATH=/path/to/your/nginx/data

#
# NGINX use special conf files 
#
# In case you want to add some special configuration to your NGINX Web Proxy you could 
# add your files to ./conf.d/ folder as of sample file 'uploadsize.conf'
#
# [WARNING] This setting was built to use our `start.sh`.
#
# [WARNING] Once you set this options to true all your files will be copied to data
#           folder (./data/conf.d). If you decide to remove this special configuration
#           you must delete your files from data folder ./data/conf.d. 
#
#USE_NGINX_CONF_FILES=true

#
# Docker Logging Config
#
# This section offers two options max-size and max-file, which follow the docker documentation
# as follow:
#
# logging:
#      driver: "json-file"
#      options:
#        max-size: "200k"
#        max-file: "10" 
#
#NGINX_WEB_LOG_MAX_SIZE=4m
#NGINX_WEB_LOG_MAX_FILE=10

#NGINX_GEN_LOG_MAX_SIZE=2m
#NGINX_GEN_LOG_MAX_FILE=10

#NGINX_LETSENCRYPT_LOG_MAX_SIZE=2m
#NGINX_LETSENCRYPT_LOG_MAX_FILE=10
$ run ./start.sh 
# Start the container with:
$ docker run -d -e VIRTUAL_HOST=domain.com \
-e LETSENCRYPT_HOST=domain.com \
-e LETSENCRYPT_EMAIL=email@domain.com \
--network=webproxy \
--name deconstruct_chat \
httpd:alpine
Having installed Nginx, time for Rocket.chat
Inspired by this exchange

$ mkdir rocket
$ cd rocket
$ touch docker-compose.yml
$ sudo nano docker-compose.yml
Copy from example file (docker-compose.yml) and replace with your domain and email info:

version: '3.3'

services:
  db:
    image: mongo
    volumes:
      - ./datatest/runtime/db:/data/db
      - ./datatest/dump:/dump
    command: mongod --smallfiles

  rocketchat:
    image: rocketchat/rocket.chat:latest
    environment:
      MONGO_URL: mongodb://db:27017/rocketchat
      ROOT_URL: http://domain.com
      Accounts_UseDNSDomainCheck: "false"
      MAIL_URL: smtp://email@domain.com
      VIRTUAL_HOST: domain.com
      LETSENCRYPT_HOST: domain.com
      LETSENCRYPT_EMAIL: email@domain.com
    links:
      - db:db
    ports:
      - 3000:3000
    restart: always
  #hubot:
    # doesnt matter for now
networks:
    default:
       external:
         name: mywebproxy
$ docker-compose build
$ docker-compose up -d
And that’s it! I’m sure there are tweaks to be done, but for a first time install I think it went pretty well. The team managed to log on and everything appears to be working a few days on 😉

Let me know if you know of any problems as, like I mentioned, I’m new to Docker and still learning.

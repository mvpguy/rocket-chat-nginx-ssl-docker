# rocket-chat-nginx-ssl-docker
A step-by-step guide to installing Rocket.chat with Nginx and an SSL certificate

Here is what worked for me:

I started with a Digital Ocean container with a one-click install of Docker pre-installed.

1. Installing nginx + SSL (from Let’s encrypt free SSL certificate)
This step was based on this repo for a getting set up with Nginx with auto SSL from Let’s Encrypt:

```
mkdir nginx
cd nginx
mkdir data 
mkdir ssl
cd ssl
git clone https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git
cd docker-compose-letsencrypt-nginx-proxy-companion
#create and modify .env file to add IP and networname
$ touch .env
$ sudo nano .env
```

Copy file from example.env and modify your specific details (IP)

```
$ run ./start.sh 
# Start the container with:
$ docker run -d -e VIRTUAL_HOST=domain.com \
-e LETSENCRYPT_HOST=domain.com \
-e LETSENCRYPT_EMAIL=email@domain.com \
--network=webproxy \
--name deconstruct_chat \
httpd:alpine
```

Having installed Nginx, time for Rocket.chat

```
$ mkdir rocket
$ cd rocket
$ touch docker-compose.yml
$ sudo nano docker-compose.yml
```

Copy from example file (docker-compose.yml) and replace with your domain and email info:

```
$ docker-compose build
$ docker-compose up -d
```


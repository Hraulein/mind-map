# Simple mind map (Docker Containers)

[![Build Status](https://github.com/hraulein/mind-map/workflows/Multi-Platform%20Docker%20Build/badge.svg)](https://github.com/hraulein/mind-map/actions)
[![Docker Version](https://img.shields.io/docker/v/hraulein/mind-map/latest)](https://github.com/hraulein/mind-map/)
[![Docker Pulls](https://img.shields.io/docker/pulls/hraulein/mind-map)](https://hub.docker.com/r/hraulein/mind-map/)
[![Docker Image Size](https://img.shields.io/docker/image-size/hraulein/mind-map/latest)](https://hub.docker.com/r/hraulein/mind-map/)
[![GitHub issues](https://img.shields.io/github/issues/hraulein/mind-map)](https://github.com/hraulein/mind-map/issues)
[![npm-version](https://img.shields.io/npm/v/simple-mind-map)](https://www.npmjs.com/package/simple-mind-map)
![license](https://img.shields.io/npm/l/express.svg)

--- 

[中文](./README.md) | English

Building cross platform HTTP services based on the Gin framework, utilizing Docker's multi platform building capabilities to enable mind maps to run and be used on different hardware architectures.

- Cross platform compatibility: Supports `AMD64` `arm64 | arm/V8` `arm/V7`  
- Ready to use out of the box: pre configured mind map static file, no additional runtime dependencies required

Online address: https://mind-map.hraulein.com
Mirror address: https://hub.docker.com/r/hraulein/mind-map

If you encounter issues such as image startup failure during deployment, please raise an issue or contact email: solitude@hraulein.com

- The current operating environment of the container is `Scratch` (excluding `sh/bash`), which does not affect the running of `Mind Map`
To mount the static file of your custom mind map, map your file directory to the `/app` inside the container
- At present, `httpdGIN` reads configurations in the form of configuration files. If you need to customize configurations, please copy the `/conf.d/` directory inside the container before mounting it

## Usage

1\. `docker-compose.yaml`

```
services:
  mind-map:
    image: hraulein/mind-map:latest
    container_name: mind-map
    restart: always
    ports:
      - "8080:8080"  
    volumes:                   
      - ./your_config_dir:/conf.d
  #   - ./your_dist_dir:/app    # If you want custom mind-map static dir


```

2\. `docker cli`

```
docker run -d --name mind-map -p 8080:8080 -v ./your_config_dir:/conf.d hraulein/mind-map:latest
```

## Nginx configuration reference

- HTTP redirection HTTPS

```
# /etc/nginx/conf.d/00-redirect.conf

server {
    listen 80 default_server;
    listen [::]:80 default_server;
    return 301 https://$host$request_uri;
}
```

- SSL certificate related configuration

``` 
# /etc/nginx/conf.d/include/ssl_parameter

ssl_certificate '/etc/nginx/*****/*****/fullchain.cer';    # <<< replace file path
ssl_certificate_key '/etc/nginx/*****/*****/*****.key';    # <<< replace file path
ssl_trusted_certificate '/etc/nginx/*****/*****/ca.cer';   # <<< replace file path
ssl_session_cache shared:SSL:1m;
ssl_session_timeout 10m;
ssl_session_tickets off;
ssl_prefer_server_ciphers on;
ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256';
ssl_protocols TLSv1.2 TLSv1.3;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 1.1.1.1 valid=300s;
resolver_timeout 5s;
add_header Strict-Transport-Security "max-age=31536000" always;
```

- Nginx reverse proxy 

``` 
# /etc/nginx/conf.d/mind-map.conf

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    http2 on;
    include ./conf.d/include/ssl_parameter;  
  
    server_name mind-map.hraulein.localhost;   # <<< replace your domain
    set $IPADDR 172.16.19.156;                 # <<< replace localhost ipaddr

    location / {
        proxy_pass http://$IPADDR:8080;        # <<< replace your service port
        proxy_set_header Host $host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection upgrade;
        proxy_set_header Accept-Encoding gzip;
    }    
    # include ./conf.d/include/err_pages;
}
```

## Appreciate

If docker images are helpful to you, why not treat me to a Starbucks to satisfy your cravings~

![Image](https://github.com/user-attachments/assets/a27ed620-30a3-460d-85b2-6fa869a91780)



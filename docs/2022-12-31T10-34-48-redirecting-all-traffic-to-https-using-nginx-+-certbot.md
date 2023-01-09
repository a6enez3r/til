---
layout: default
date: 2022-12-31T10:34:48-05:00
title: Redirecting All Traffic To HTTPS Using Nginx + Certbot
tags: 
---

# Redirecting All Traffic To HTTPS Using Nginx + Certbot

- given a super vanilla `nginx` config like the one below

```json
server {
  listen        80;
  server_name   abenezer.sh www.abenezer.sh abmamo.com www.abmamo.com;
  location / {
    proxy_pass  http://localhost:3333;
  }
}
```

you can use certbot to redirect all inbound connections to be redirected to HTTPS.

- generate certificate using `certbot`

```bash
    sudo certbot -d abenezer.sh -d www.abenezer.sh -d abmamo.com -d www.abmamo.com
```

`NOTE`: if using DigitalOcean make sure the domain records point to the server you are running this command on or the ACME challenges will fail

- the above will generate an `nginx` config that looks something like

```json
server {
  server_name   abenezer.sh www.abenezer.sh abmamo.com www.abmamo.com;
  location / {
    proxy_pass  http://localhost:3333;
  }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/abenezer.sh/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/abenezer.sh/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = www.abmamo.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = abmamo.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = www.abenezer.sh) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = abenezer.sh) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


  listen        80;
  server_name   abenezer.sh www.abenezer.sh abmamo.com www.abmamo.com;
    return 404; # managed by Certbot
}
```
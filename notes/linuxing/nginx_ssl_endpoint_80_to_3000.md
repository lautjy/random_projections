# nginx config basics


## Install
`sudo apt-get install nginx`

## SSL port to local port 3000

Edit this `/etc/nginx/sites-available`:

```
#################################
# HTTP redirect to HTTPS
#################################
server {
  listen         80;
  server_name    jelpp-ai-statsd.baronatest.fi;
  return         301 https://$server_name$request_uri;
}
#################################
# {{ service }} HTTPS configuration
#################################
server {
  # added these
  root /data/nginx/html;
  error_page 404 500 502 503 /error.html;

  # these added for SSL (using a self signed certificate)
  listen 443 ssl;
  server_name jelpp-ai-statsd.baronatest.fi;
  ssl_certificate /etc/ssl/certs/baronatest.fi.2014.crt;
  ssl_certificate_key /etc/ssl/private/baronatest.fi.2014.key;

  # ssl_protocols SSLv3 TLSv1;
  # ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv3:+EXP;
  # ssl_prefer_server_ciphers on;

  location = /robots.txt {
    alias /data/nginx/html/robots.txt;
  }

  location / {
    try_files $uri @node;
  }

  location @node {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header X-Forwarded-Proto https;
    proxy_redirect off;
  }
}
```

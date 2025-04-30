# 自架NAS  NGINX 反向代理
## 必需要有docker,portainer
1. mkdir nginx
2. cd nginx
3. nano config.json
```
{ 
  "database": { 
     "engine": "mysql", 
     "host": "db", 
     "name": "npm", 
     "user": "npm", 
     "password": "npm", 
     "port": 3306
   }
 }

```
4. nano docker-compose.yml
```
--- 
version: '3' 
services: 
  app: 
    image: 'jc21/nginx-proxy-manager:latest' 
    ports: 
      - '80:80' #HTTP Traffic 
      - '81:81' #Dashboard Port
      - '443:443' #HTTPS Traffic 
    volumes: 
      - ./config.json:/app/config/production.json 
      - ./data:/data 
      - ./letsencrypt:/etc/letsencrypt
  db: 
    image: 'jc21/mariadb-aria:10.4.15-innodb'
    environment: 
      MYSQL_ROOT_PASSWORD: 'npm' 
      MYSQL_DATABASE: 'npm' 
      MYSQL_USER: 'npm' 
      MYSQL_PASSWORD: 'npm'
    volumes:
      - ./data/mysql:/var/lib/mysql

```
### 進去 sudo nano /etc/nginx/nginx.conf
在http內增加以下
```
 include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
        client_max_body_size 50M;
        client_header_timeout 3600s;
        client_body_timeout 3600s;
        fastcgi_connect_timeout 3600s;
        fastcgi_send_timeout 3600s;
        fastcgi_read_timeout 3600s;
```
完整
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;
        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
        client_max_body_size 50M;
        client_header_timeout 3600s;
        client_body_timeout 3600s;
        fastcgi_connect_timeout 3600s;
        fastcgi_send_timeout 3600s;
        fastcgi_read_timeout 3600s;
}

#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
#
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}



```
5. 初始帳號:admin@example.com
6. 先用好CloudFlare與 Duckdns DNS 
7. 申請SSL 需要 CloudFlare API 
8. 之後修正conf 去docker 內更改
```
docker exec -it nginx-app-1 /bin/sh

```


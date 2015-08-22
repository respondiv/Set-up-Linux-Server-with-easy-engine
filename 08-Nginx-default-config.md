#### Default Nginx Config for Optimized Website

**check # of cpu, the # of worker_processes value (below) depends upon # of CPU**

`sudo grep processor /proc/cpuinfo | wc -l`

**Edit Nginx default config**

`sudo nano /etc/nginx/nginx.conf`

Make necessary changes so it looks like following

```
user www-data;
worker_processes auto;
worker_rlimit_nofile 100000;
pid /run/nginx.pid;

events {
        # add epoll
        use epoll;
        worker_connections 4096;
        multi_accept on;
}

http {
        ##
        # EasyEngine Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 30;
        types_hash_max_size 2048;

        server_tokens off;
        reset_timedout_connection on;
        add_header X-Powered-By "EasyEngine 3.3.4";
        add_header rt-Fastcgi-Cache $upstream_cache_status;

        # Limit Request
        limit_req_status 403;
        limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

        # Proxy Settings
        # set_real_ip_from      proxy-server-ip;
        # real_ip_header        X-Forwarded-For;

        fastcgi_read_timeout 300;
        client_max_body_size 100m;

        ##
        # SSL Settings
        ##

        ssl_session_cache shared:SSL:20m;
        ssl_session_timeout 10m;
        ssl_prefer_server_ciphers on;

        #comment original ssl_ciphers and paste following
        #ssl_ciphers HIGH:!aNULL:!MD5:!kEDH;

        ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

       ##
        # Basic Settings
        ##

        #uncomment following
        server_names_hash_bucket_size 64;
        server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        # Log format Settings
        log_format rt_cache '$remote_addr $upstream_response_time $upstream_cache_status [$time_local] '
        '$http_host "$request" $status $body_bytes_sent '
        '"$http_referer" "$http_user_agent"';

        ##
        # Gzip Settings
        ##

        gzip on;
        gzip_disable "msie6";

        gzip_vary on;
        gzip_proxied any;
        gzip_comp_level 6;
        gzip_buffers 16 8k;
        gzip_http_version 1.1;
        gzip_types
            application/atom+xml
            application/javascript
            application/json
            application/rss+xml
            application/vnd.ms-fontobject
            application/x-font-ttf
            application/x-web-app-manifest+json
            application/xhtml+xml
            application/xml
            font/opentype
            image/svg+xml
            image/x-icon
            text/css
            text/plain
            text/x-component
            text/xml
            text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
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

**Comment all the lines from the file** `/etc/nginx/conf.d/ee-nginx.conf` **as the above configuration contain everything**

`sudo nano /etc/nginx/conf.d/ee-nginx.conf`

**Test and Restart nginx**

`sudo nginx -t && sudo /etc/init.d/nginx restart`

OR

`sudo ee stack restart`

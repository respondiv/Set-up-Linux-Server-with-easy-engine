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
  
  server_tokens off;
  reset_timedout_connection on;
  add_header X-Powered-By "EasyEngine 2.2.2";
  add_header rt-Fastcgi-Cache $upstream_cache_status;
  
  # Limit Request
  limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
  
  # must add
  limit_req_zone $binary_remote_addr zone=php:10m rate=2r/s;
  limit_req_status 429;
  limit_conn_status 429;
  
  # Proxy Settings
  # set_real_ip_from proxy-server-ip;
  # real_ip_header X-Forwarded-For;
  
  fastcgi_read_timeout 300;
  
  # Change this to the desired max upload limit
  client_max_body_size 100m;
  
  # SSL Settings
  ssl_session_cache shared:SSL:20m;
  ssl_session_timeout 10m;
  ssl_prefer_server_ciphers on;
  
  # comment the original ssl_ciphers and replace it with
  ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
  
  
  ##
  # Basic Settings
  ##
  sendfile on;
  tcp_nopush on;
  tcp_nodelay on;
  keepalive_timeout 30;
  types_hash_max_size 2048;
  # server_tokens off;
  
  # uncomment these lines
  server_names_hash_bucket_size 64;
  server_name_in_redirect off;
  
  include /etc/nginx/mime.types;
  default_type application/octet-stream;
  
  ##
  # Logging Settings
  ##
  access_log /var/log/nginx/access.log;
  error_log /var/log/nginx/error.log;
  log_format rt_cache '$remote_addr $upstream_response_time
  $upstream_cache_status [$time_local] '
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
  gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
  
  ##
  # Virtual Host Configs
  ##
  
  include /etc/nginx/conf.d/*.conf;
  include /etc/nginx/sites-enabled/*;

}
```

#### Comment all the lines from the file `/etc/nginx/conf.d/ee-nginx.conf` as the above configuration contain everything

`sudo nano /etc/nginx/conf.d/ee-nginx.conf`

**Test and Restart nginx**

`sudo nginx -t && sudo /etc/init.d/nginx restart`



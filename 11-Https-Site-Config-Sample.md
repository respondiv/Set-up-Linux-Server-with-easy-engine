#### Https Site Config  -- After installing SSL Certificate

**Edit Server Block Config File**

`sudo nano /etc/nginx/sites-available/example.com` or `sudo ee site edit example.com`

Modify the file as follows

```
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  
  # Make site accessible from http://example.com/
  server_name example.com www.example.com;
  
  # sends all traffic from http to https (only when ssl enabled)
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl default_server spdy;
  listen [::]:443 ssl default_server spdy;  
  
  # Make site accessible from http://example.com/
  server_name example.com www.example.com;
  
  access_log /var/log/nginx/example.com.access.log rt_cache;
  error_log /var/log/nginx/example.com.error.log;
  
  root /var/www/example.com/htdocs;
  index index.php index.htm index.html;
  
  include common/wpfc.conf;
  include common/wpcommon.conf;
  include common/locations.conf;
  
  # Specify a character set
  charset utf-8;
  
  # ssl settings begin
  ssl on;
  ssl_certificate /etc/nginx/example.com-ssl/example.com.combined.crt;
  ssl_certificate_key /etc/nginx/example.com-ssl/example.com.key;
  
  #enables all versions of TLS, but not SSLv2 or 3 which are weak and now deprecated.
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  
  # Add-on: OCSP Stapling - recommend by yoast.com
  ssl_stapling on;
  ssl_stapling_verify on;
  resolver 8.8.8.8 8.8.4.4 valid=300s;
  resolver_timeout 10s;
  
  # This forces every request after this one to be over HTTPS
  add_header Strict-Transport-Security "max-age=31536000";
  
  # ssl settings ends
  
  # forward the page to serve via spdy if the server request non-spdy page request
  add_header Alternate-Protocol 443:npn-spdy/3;
  
  # SECURITY
  
  # Ignore other host headers
  if ($host !~* ^(example.com|www.example.com)$ ) {
  return 444;
  }
  
  # Only allow GET , POST, and HEAD
  if ($request_method !~ ^(GET|POST|HEAD)$ ) {
  return 444;
  }
  
  #hide phpmyadmin folder or any other folder
  #location ~ ^/phpmyadmin {
  # Allow from static ip (this is a current server ip)
  #allow 104.236.4.61;
  #deny all;
  #return 404;
  #}
  
  # prevent brute force
  #location = /wp-login.php { #use this for single url
  #location ~ ^/wp-admin { #use this for whole folder
  # fastcgi_pass unix:/var/run/php5-fpm.sock;
  # fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
  # include fastcgi_params;
  # limit_req zone=php burst=1 nodelay;
  #}

}

```

**Test and Restart nginx**

`sudo nginx -t && sudo /etc/init.d/nginx restart`

OR

`sudo ee stack restart`

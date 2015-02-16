#### Http Site Config - Without SSL

**Edit Server Block Config File**

`sudo nano /etc/nginx/sites-available/example.com` or `sudo ee site edit example.com`

Modify the file as follows

```
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  
  # Make site accessible from http://example.com/
  server_name example.com www.example.com;
  
  # sends all traffic from www.example.com to example.com
  return 301 http://$server_name$request_uri;
  
  access_log /var/log/nginx/example.com.access.log rt_cache;
  error_log /var/log/nginx/example.com.error.log;
  
  root /var/www/example.com/htdocs;
  index index.php index.htm index.html;
  
  include common/wpfc.conf;
  include common/wpcommon.conf;
  include common/locations.conf;
  
  # Specify a character set
  charset utf-8;
  
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

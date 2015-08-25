#### WordPress + Fast CGI cashe + Wocommerce Settings

** Create a file wpfcwc.conf on /etc/nginx/common**

`sudo nano /etc/nginx/common/wpfcwc.conf`

Copy and Paste following into the file

```
# WPFC NGINX CONFIGURATION for Woocommerce

set $skip_cache 0;

# POST requests and URL with a query string should always go to php
if ($request_method = POST) {
  set $skip_cache 1;
}

# Skip cache for query string  
if ($query_string != "") {
  set $skip_cache 1;
}

# Don't cache URL containing the following segments
if ($request_uri ~* "(/wp-admin/|/xmlrpc.php|wp-.*.php|index.php|/feed/|sitemap(_index)?.xml|[a-z0-9_-]+-sitemap([0-9]+)?.xml)") {
  set $skip_cache 1;
}

# Don't use the cache for logged in users or recent commenter
if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_no_cache|wordpress_logged_in") {
  set $skip_cache 1;
}

# Skip cache on WooCommerce pages
if ($request_uri ~* "/store.*|/cart.*|/my-account.*|/checkout.*|/addons.*") {
  set $skip_cache 1;
}

# Skip cache for WooCommerce query string
if ( $arg_add-to-cart != "" ) {
  set $skip_cache 1;
}  

# Skip cache when WooCommerce cart is not empty
if ( $cookie_woocommerce_items_in_cart = "1" ){
  set $skip_cache 1;
}

# Use cached or actual file if they exists, Otherwise pass request to WordPress
location / {
  try_files $uri $uri/ /index.php?$args;
}


location ~ ^/wp-content/cache/minify/(.+\.(css|js))$ {
  try_files $uri /wp-content/plugins/w3-total-cache/pub/minify.php?file=$1;
}


location ~ \.php$ {
  
  set $rt_session "";
    
  if ($http_cookie ~* "wp_woocommerce_session_[^=]*=([^%]+)%7C") {
    set $rt_session wp_woocommerce_session_$1;
  } 

  if ($skip_cache = 0 ) {
    more_clear_headers "Set-Cookie*";
    set $rt_session "";
  }
  
  fastcgi_cache_key "$scheme$request_method$host$request_uri$rt_session";

  try_files $uri =404;
  include fastcgi_params;
  fastcgi_pass php;
  fastcgi_cache_bypass $skip_cache;
  fastcgi_no_cache $skip_cache;
  fastcgi_cache WORDPRESS;
}

location ~ /purge(/.*) {
  fastcgi_cache_purge WORDPRESS "$scheme$request_method$host$1";
}
```

Press `ctrl+x` and type `y` to save and exit

**Edit Server Block Config File**

`sudo nano /etc/nginx/sites-available/example.com` or `sudo ee site edit example.com`

Find and replacefollowing

```
# find
include common/wpfc.conf;

#replace with
include common/wpfcwc.conf;

```

**Test and Restart nginx**

`sudo nginx -t && sudo /etc/init.d/nginx restart`

OR

`sudo ee stack restart`


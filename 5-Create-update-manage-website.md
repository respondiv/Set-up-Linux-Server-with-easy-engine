#### Few Setup before installing WordPress

Edit `/etc/easyengine/ee.conf` file to setup custom wordpress

`sudo nano /etc/easyengine/ee.conf`

Add / Set following values

```
db-name = true
db-user = true
prefix = true
user = new-wordpress-admin-user-name
password = my-secure-password
email = admin-email@example.com
```

Press `ctrl+x` and type `y` to save and exit

#### Create a WordPress Website

Create wordPress with fast cgi cache which uses W3TC for object and database cache only. This is the best compared to all caches handled by W3TC


`sudo ee site create example.com --wpfc`

Change example.com to your domain name or ip address

#### Create non WordPress Site

create example.com for static/html sites

`sudo ee site create example.com --html`

create example.com with php support 

`sudo ee site create example.com --php`

create example.com with php & mysql support

`sudo ee site create example.com --mysql`


###### More details for site creation can be found here: [EasyEngine](https://github.com/rtCamp/easyengine)

#### Other EasyEngine Commands

Only few selected commands are listed here.

##### Site

Site enable

`sudo ee site enable example.com`

Site disable

`sudo ee site disable example.com`

Site delete

`sudo ee site delete example.com`

Site info

`sudo ee site info example.com`

Site List

```
# list all websites
sudo ee site list

#list all enabled websites
sudo site list --enabled

# list all disabled websites
sudo site list --disabled
```

Show Nginx Configuration

`sudo ee site show example.com`

Edit Site's Nginx Configuration

`sudo ee site edit example.com`

[View instruction to migrate website](https://rtcamp.com/easyengine/docs/commands/site/migrate/)

##### Debuggins

Debug System

`sudo ee debug`

Debug Site

`sudo ee debug example.com`

Rewrites only

`sudo ee debug example.com --rewrite`

Interactive

`sudo ee debug example.com -i`

##### Clean

Clean all Cache (FastCGI, Memcache, OPCache)

`sudo ee clean --all`

Clean FastCGI cache

`sudo ee clean --fastcgi`

Clean Memcache

`sudo ee clean --memcache`

Clean OPcache

`sudo ee clean --opcache`

##### Help

CLI

`sudo ee info`

Built in help

`sudo ee help`

Manual pages

`sudo man ee`

##### Logging Path for easyengine

Log loaction

`/var/log/easyengine/`


##### Convections
File sttucture for example.com

All files related to example.com

`/var/www/esample.com/`

Document Root

`/var/www/example.com/htdocs/`

Logs

`/var/www/example.com/logs/`

WordPress Config

`/var/www/example.com/wp-config.php`

Nginx Config for example.com

`/etc/nginx/sites-available/example.com`

or

`/etc/nginx/sites-enabled/example.com`

Default nginx config

`/etc/nginx/nginx.conf`

#### Useful Links

[View all commands by visiting this link](http://docs.rtcamp.com/easyengine/commands/)

For more info see FAQ: [https://rtcamp.com/easyengine/faq/](https://rtcamp.com/easyengine/faq/)

To see Conventions & File System Layout: [https://rtcamp.com/wordpress-nginx/tutorials/conventions/](https://rtcamp.com/wordpress-nginx/tutorials/conventions)


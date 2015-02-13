#### Increase File Upload Limit

EasyEngine will increase the file upload size to 100MB automatically. Use this if you need to modify that setting.

**Edit the php config file**

`sudo nano /etc/php5/fpm/php.ini`

**Set / Update following**

Note post_max_size should be larger than upload_max_filesize

```
upload_max_filesize = 100M
post_max_size = 120M
```

**Reload php-fpm**

`sudo service php5-fpm restart`

**Test and Restart nginx**

`sudo nginx -t && sudo /etc/init.d/nginx restart`

**Reboot the system if needed**

`sudo reboot now`

#### Remove `default_server` value from `/etc/nginx/sites-available/default` aka Default Website

**Open the file for editing**

`sudo nano /etc/nginx/sites-available/default`

Modify the file as follows

```
# Before editing
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  ....
}

# After editing
server {
  listen 80;
  listen [::]:80;
  ....
}
```

#### Find a file by name

```
# syntax: Find [path] -name "filename.extension"

# e.g this search the file ee.conf in entire harddisk
find / -name "ee.conf"

# e.g this search the file wp-config.php in /var/www/example.com folder
find /var/www/example.com -name "wp-config.php"
```

Details Docs link: [https://help.ubuntu.com/community/find](https://help.ubuntu.com/community/find)

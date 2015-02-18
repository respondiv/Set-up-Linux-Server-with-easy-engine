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

#### Remove a file / folder

```
# syntax to remove a file
sudo rm path/to/file/filename.extension

# e.g
sudo rm /var/www/example.com/htdocs/file.php

# syntax to remove a folder and its content
sudo rm -r path/to/folder

# e.g
sudo rm -r /var/www/example.com/htdocs/folder1
```

#### zip / unzip all files and folder inside a directory

```
# to zip file / folder in current directory
zip -r new-zip-filename.zip path/to/the/folder*

# to zip file / folder in current directory and exclude few director
zip -r new-zip-filename.zip path/to/the/folder* -x "path/to/exclude/folder1" "path/to/exclude/folder2"

# to unzip in current directory
unzip filename.zip

# to unzip in different directory
unzip filename.zip -d path/to/destination_folder
```

#### Backup MySQL Database from command line

```
mysqldump --single-transaction -u mysql_user_name -p -mysql_db_name | gzip > filename.sql.gz
```

#### Repair and Optimize MySQL Database

```
# repair a database
mysqlcheck -r -u mysql_user_name -p -mysql_db_name

# Optimize a database
mysqlcheck -r -u mysql_user_name -p -mysql_db_nam
```

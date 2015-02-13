### Install EasyEngine Current Version 3.0.1
Easy Engine Docs: [https://rtcamp.com/easyengine/docs/](https://rtcamp.com/easyengine/docs/)
or [http://docs.rtcamp.com/easyengine/](http://docs.rtcamp.com/easyengine/)

Install wget if it's not already installed (usually is)

`sudo apt-get install wget`

Install easy engine

`sudo wget -q http://rt.cx/ee && sudo bash ee`

Above command asks for your name and email address, which will be set as your git username. Email address is used to maintain track of configuration files. Also the same email
address is used while creating WordPress website

For EasyEngine (ee) auto completion, run the following command

`source /etc/bash_completion.d/ee_auto.rc`

Install EasyEngine Stack. EasyEngine(ee) Stack manages packages required to run your website smoothly. Check this URL for the complete guide and installation of complete package

We will choose few stack that is needed for web server only, no mail server. To install these stack run following command

```
ee stack install
ee stack install --utils
ee stack install --phpmyadmin
```

###### DO not forget to save your HTTP authentication credentials as it's needed to manage admin portal via browser

#### Let's secure EasyEngine while we are at it

Change your HTTP authentication credentials

`sudo ee secure --auth`

Changing admin port to 33344 or something random (default 22222)

`sudo ee secure --port`

Whitelisting IP’s (seperate ip by comma)

`sudo ee secure --ip`

For Whitelisting IP - you can also edit / update the following file

`sudo nano /etc/nginx/common/acl.conf`

Now Add or Remove IP to whitelist e.g.

```
allow 192.168.1.0/24;
allow 127.0.0.1;
allow 192.168.1.100;
deny all;
```

Press `ctrl+x` and type `y` to save and exit

Restart Nginx, PHP, MYSQL, Postfix

`sudo ee stack restart`

or Just Restart Nginx

`sudo nginx -t && sudo /etc/init.d/nginx restart`





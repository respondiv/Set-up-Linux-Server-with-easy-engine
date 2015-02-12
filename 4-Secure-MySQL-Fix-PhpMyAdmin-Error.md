
#### Secure MySQL

locate mysql root password

`sudo cat ~/.my.cnf`

secure mysql installation

`sudo mysql_secure_installation`

Select the configure that best matches you, normally press `y` 5 times. You can update the MySQL root password here if you want to

update mysql root password for easy engine to read on following file if you have updated the password

`sudo nano ~/.my.cnf`

Save and exit the file `ctrl+x` and type `y` to save and exit

#### Check for PhPmyAdmin Error, if there is any. To check

Go to `https://1.2.3.4:33344/db/pma` using Web Browser
- Replace 1.2.3.4 with your VPS ip
- Replace :33344 with the new port that you have setup when creating easy engine (default is 22222)
- verify yourself using HTTP auth Credential
  - If you forget your credential, you can reset it. [How to reset HTTP AUTH](https://github.com/respondiv/Set-up-Linux-Server/blob/master/3-Install-Easy-Engine.md)
- Login to PhpMyAdmin using root credentials
  - Username: root
  - Password: MySQL root Password that you have setup above
- See if there is any Error, Normally there should be two
  - blowfish_secret error message
  - Can't update

#### To fix Can't Update error

Copy all the SQL queries from here [create_tables.sql](https://github.com/respondiv/Set-up-Linux-Server/blob/master/create_tables.sql)

or, you can login Using SSH and copy all the queries from `/var/www/22222/htdocs/db/pma/examples/create_tables.sql`. To do so, run following command

`cat /var/www/22222/htdocs/db/pma/examples/create_tables.sql`

Copy the display SQL queries, then perfom following steps
- Go to phpmyadmin url `https://1.2.3.4:33344/db/pma`
  - replace ip and port
- Select "SQL" menu without choosing any database
- Paste the queries copied from create_tables.sql file and click go
  - You should see the message "Tables created successfully"

Now go back to SSH, we need to edit `config.inc.php` file

`sudo nano /var/www/22222/htdocs/db/pma/config.inc.php`

Make following Changes
- Add blowfish_secret, which is a random word(s)
  - this will fix the blowfish_secret error
- uncomment all storage database and tables config
 Save and exit the file `ctrl+x` and type `y` to save and exit

Go to the Web Browser, Logout from current session of phpMyAdmin and login back. The errors should be fixed





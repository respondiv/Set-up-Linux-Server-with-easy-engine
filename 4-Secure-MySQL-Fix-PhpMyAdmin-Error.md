
#### Secure MySQL

locate mysql root password

`sudo cat ~/.my.cnf`

secure mysql installation

`sudo mysql_secure_installation`

Select the configure that best matches you, normally press `y` 5 times. You can update the MySQL root password here if you want to

update mysql root password for easy engine to read on following file if you have updated the password

`sudo nano ~/.my.cnf`

Save and exit the file `ctrl+x` and type `y` to save and exit

#### Fix PhPmyAdmin Error if there is any

Go to `https://1.2.3.4:33344/db/pma`
- Replace 1.2.3.4 with your vps ip
- Replace :33344 with the new port that you have setup when creating easy engine (default is 22222)
- verify yourself using HTTP auth Credential
  - You can reset this credentials. [How to reset HTTP AUTH](https://github.com/respondiv/Set-up-Linux-Server/blob/master/3-Install-Easy-Engine.md)
- Login to PhpMyAdmin wusing root credentials
  - Username: root
  - Password: MySQL root Password that you have setup above
- See if there is any Error

#### Create new SFTP user

**Create a new user**

`sudo adduser new-sudo-user`

**Set / Reset Password**

`sudo passwd new-sudo-user`

**Change User’s Home Directory**

`sudo usermod --home /var/www/example.com/htdocs new-sudo-user`

**Add user to the group www-data, which is a nginx web-server group**

`sudo usermod -a -G www-data new-sudo-user`

*If needed, create a new group for users, don't need for this setup, so skip this*

`sudo groupadd new-group-name`

**See the current permission**

```
sudo ls -l /var/www/example.com
sudo ls -l /avr/www/example.com/htdocs
sudo ls -l /avr/www/example.com/htdocs/wp-content
```

**Add group read/write `g+rw`**

```
sudo chmod -R g+rw /var/www/example.com
sudo chmod -R 755 /var/www/example.com/htdocs/wp-content
```

**Check folder permission using `ls -l`, it should be like: `drwxrwxr-x`**

```
sudo ls -l /var/www/example.com
sudo ls -l /avr/www/example.com/htdocs
sudo ls -l /avr/www/example.com/htdocs/wp-content
```

#### Give this new-user a SSH access with sudo 

**Open the `visudo` to give access**

`visudo`

Find the section with the following

```
# User privilege specification
root ALL=(ALL:ALL) ALL
```

Add following below the `root ALL=(ALL:ALL) ALL` line

```
# User privilege specification
root ALL=(ALL:ALL) ALL
new-user ALL=(ALL:ALL) ALL
```

Press `ctrl+x` and type `y` to save and exit

**To Configure SSH Server edit `/etc/ssh/sshd_config`**

`sudo nano /etc/ssh/sshd_config`

Find the following and add / change the information where applicable. 

###### Change the SSH port for Security.

```
# change SSH port from 22, for security
Port 22233
Protocol 2
# this restrict root login, for security
PermitRootLogin no 
.......
........
# add this at the end of the file
UseDNS no
AllowUsers new-user
```

Press `ctrl+x` and type `y` to save and exit

**Reload SSH**

`service ssh restart`

**If necessary reboot**

`sudo reboot now`







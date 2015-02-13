#### Install SSL certificate using 3rd party SSL Certificate Provider

**Create a folder to store SSL**

`sudo mkdir /etc/nginx/example.com-ssl/`

**We need to perform next few steps within director, so change the directory**

`cd /etc/nginx/example.com-ssl/`

**Create a private Server Key (don't forget to store the passphrase)**

`sudo openssl genrsa -des3 -out example.com.key 2048`

*It will ask passphrase, make it hard and keep it secure*

**Create a certificate signing request**

`sudo openssl req -new -key example.key -out example.com.csr`

*Note: in Common Name Place enter domain name or server ip. e.g.* **Common Name: example.com**

**Remove Passphrase**

Although having the passphrase in place does provide heightened security, the issue starts when one tries to reload nginx. In the event that nginx, crashes or needs to reboot, you will always have to re-enter your passphrase to get your entire web server back online.

Use this command to remove the password

```
# backup the original key just in case
sudo cp example.com.key example.com.original.key

# Remove PassPhrase
sudo cp example.com.key example.com.org
sudo openssl rsa -in example.com.org -out example.com.key
```

**Copy CSR**

Ee will need to copy-paste CSR when submitting Certificate Requrst to Certificate Authority (CA). To print CSR content, use following command

`cat example.com.csr`

**Go to the CA website and request certificate by pasting CSR. [startssl.com](http://www.startssl.com/) provides free SSL**

Obtain SSL Certificate, will be in .crt or .pem format. Combine all .crt file to 1 file should be in following oder

`original-certificate > intermediate certificate > root certificate (if any)`

You can combine all of them in text editor, let's call it example.com.combined.crt. We need to add this certificate to our server.

**SSH into the terminal and go to the SSL folder**

`cd /etc/nginx/example.com-ssl/`

**Add the combined certificate to the folder**

`sudo nano example.com.combined.crt`

It will open a blank file, copy and paste the certificate (right Click) here.

Press `ctrl+x` and type `y` to save and exit


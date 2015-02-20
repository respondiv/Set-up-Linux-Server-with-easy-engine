#### Generate / Install SSL certificate 

**Create a folder to store SSL**

`sudo mkdir /etc/nginx/example.com-ssl/`

**We need to perform next few steps within director, so change the directory**

`cd /etc/nginx/example.com-ssl/`

#### Generate your own SSL certificate and Key

It's secure and free of cost but the browser will display warning message saying "Your connection is not Private" as its security certificate is not trusted, but it didn't come from recognized company.

`sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout example.com.key -out example.com.crt`

**Skip Rest of the Steps**

#### Using a 3rd Party Provider like [startssl.com](http://www.startssl.com/) or [comodo ssl](https://ssl.comodo.com/) to get a SSL certificate

There is a cost to it but the browser will not display warning message as the security certificate is trusted because you are getting it from recognized company.


**Create a private Server Key (don't forget to store the passphrase)**

`sudo openssl genrsa -des3 -out example.com.key 2048`

*It will ask passphrase, make it hard and keep it secure*

**Create a certificate signing request**

`sudo openssl req -new -key example.com.key -out example.com.csr`

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

We will need to copy-paste CSR when submitting Certificate Requrst to Certificate Authority (CA). To print CSR content, use following command

`cat example.com.csr`

Copy the CSR content

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

**You'll need the CSR file (example.com.csr), the key file (example.com.key) and the combined certificate file (example.com.combined.crt) in your Server's SSL folder to use SSL for that site**

Once you have all these files, you are ready to use SSL

# Lab2 Certificate Authority Hierarchy
  
## Content
  - [Introduction](https://github.com/Yu-Tsern/DigitalCertificateLab/tree/master/lab2#introduction)
  - [Network Topology](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#network-topology)
  - [Certificates Issuance](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#certificate-issuance)
  - [LAMP Installation](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#lamp-installation)
    - [MySQL](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#install-mysql)
    - [Apache](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#install-apache)
    - [PHP](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#install-php)
    - [Simple PHP websites](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#write-a-simple-php-website)
    - [Apache SSL connection](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#enable-apache-ssl-connection)
  - [Certificate Issuance Test](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#certificate-issuance-test)
    - [CURL](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#curl)
    - [Browser](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab2/README.md#browser)


## Introduction

![Alt text](pic/Picture37.png?raw=true "Title")

As the picture shows, certificate authorities(CAs) are organized in a tree structure. Trust is propagated from the root to leaves. A certificate is trustworthy as long as it is signed by a trusted superior CA. Thus, when verifying a certificate, all certificates of the CAs involoving in this path of trust are required. 

In this lab, you will simulate the issuance of certificates within this structure, and test their validities through a SSL connection from client(**user**) to server(**ws**,) which means most of the contents are similar to what you did in the first lab. 


## Network topology

![Alt text](pic/Picture1.png?raw=true "Title")

1. **ca1** will be the root CA in this experiment. 
2. **ca2** will be the intermediate CA, that is, the subordinate CA of **ca1**. 
3. **ca3** will be the local CA, that is, the subordinate CA of **ca2**.
4. **ws** will be the web server where LAMP is installed.
5. **user** will be the client connecting to **ws**.

**_Notice: Not until all the GENI nodes turn green can you continue the following steps. This may take a while._**

## Certificate Issuance

###### Root CA

Similar to the first lab, you will have to create and initilize some files in order to ensure the configuration file will run smoothly. Connect to **ca1** and run the following commands.

```
sudo su
touch /etc/ssl/index.txt
echo 01 > /etc/ssl/serial
mkdir /etc/ssl/newcerts
```

Then, generate a key pair 

```
openssl genrsa -des3 -out /etc/ssl/private/ca1.key 2048
```

You will be asked to set a 4 to 1023 characters passphrass. Memorize it. It will be used when signing **ca2**'s CSR.

```
Generating RSA private key, 2048 bit long modulus
....................................................................................................................................+++
..................+++
e is 65537 (0x10001)
Enter pass phrase for /etc/ssl/private/ca1.key:
Verifying - Enter pass phrase for /etc/ssl/private/ca1.key:
```

Generate a self-signed certificate with that private key.

```
openssl req -key /etc/ssl/private/ca1.key -new -x509 -days 365 -out /etc/ssl/ca1.crt
```

Fill in the information of that certificate. **_Note that the common name does not matter if the node is not going to host any server. Thus, you can pick whatever name you want for CAs, but the common name for web server should be consistent with your settings. For simplicity, the common names used in the example will correspond to the names of nodes, that is jhuca1.edu, jhuca2.edu, jhuca3.edu, and jhuws.edu_**.

```
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:MD
Locality Name (eg, city) []:Baltimore
Organization Name (eg, company) [Internet Widgits Pty Ltd]:JHU
Organizational Unit Name (eg, section) []:ISI
Common Name (e.g. server FQDN or YOUR name) []:jhuca1.edu         
Email Address []:
```

Finally, edit the directory in "openssl.cnf" line 42:

```
[ CA_default ]
 
 dir             = /etc/ssl              # Where everything is kept
 certs           = $dir/certs            # Where the issued certs are kept
 crl_dir         = $dir/crl              # Where the issued crl are kept
 database        = $dir/index.txt        # database index file.
 #unique_subject = no                    # Set to 'no' to allow creation of
 ```

###### Intermediate CA

Comparing to the first lab, an intermediate CA, on the one hand, generates certificate signing request(CSR) and send it to its superior CA just like **ws**; on the other hand, it signs its subordiate CA's CSRs like **ca**. Therefore, tasks to be done on **ca2** are the combination of those you did on **ws** and **ca** previously. First, connect to **ca2** and do the followings.

```
sudo su
touch /etc/ssl/index.txt
echo 01 > /etc/ssl/serial
mkdir /etc/ssl/newcerts
```

Then, generate a private key

```
openssl genrsa -des3 -out /etc/ssl/private/ca2.key 2048
```

Create a CSR of this key

```
openssl req -new -days 3650 -key /etc/ssl/private/ca2.key -out /etc/ssl/ca2.csr
```

Similarly, you will have to put in some information. Then, edit the directory in "openssl.cnf" line 42:

```
[ CA_default ]
 
 dir             = /etc/ssl              # Where everything is kept
 certs           = $dir/certs            # Where the issued certs are kept
 crl_dir         = $dir/crl              # Where the issued crl are kept
 database        = $dir/index.txt        # database index file.
 #unique_subject = no                    # Set to 'no' to allow creation of
 ```

###### Local CA

The only difference between local CAs and the intermediate ones is the CSR they sign. Local CAs sign CSR belonging to web server, while intermediate CAs sign CSRs from subordinate CAs. Thus, the setup is pretty much the same. Connect to **ca3** and do run these commands.

```
sudo su
touch /etc/ssl/index.txt
echo 01 > /etc/ssl/serial
mkdir /etc/ssl/newcerts
```

Generate a private key

```
openssl genrsa -des3 -out /etc/ssl/private/ca3.key 2048
```

Create CSR of this key

```
openssl req -new -days 3650 -key /etc/ssl/private/ca3.key -out /etc/ssl/ca3.csr
```

Similarly, you will have to put in some information. Then, edit the directory in "openssl.cnf" line 42:

```
[ CA_default ]
 
 dir             = /etc/ssl              # Where everything is kept
 certs           = $dir/certs            # Where the issued certs are kept
 crl_dir         = $dir/crl              # Where the issued crl are kept
 database        = $dir/index.txt        # database index file.
 #unique_subject = no                    # Set to 'no' to allow creation of
 ```

###### Web Server

The web server won't function as a certificate authority, thus, just generate a private key and a corresponding CSR.

```
sudo openssl genrsa -des3 -out /etc/ssl/private/ws.key 2048
sudo openssl req -new -days 3650 -key /etc/ssl/private/ws.key -out /etc/ssl/ws.csr
```

###### Sign certificates

First, move intermediate CA's CSR to the root CA, that is, use WinSCP/SFTP or other method to transfer "ca2.csr" from **ca2** to **ca1**. 

![Alt text](pic/Picture38.png?raw=true "Title")

Sign "ca2.csr" with the following command

```
openssl ca -extensions v3_ca -in ca2.csr -config /etc/ssl/openssl.cnf -days 3000 -out ca2.crt -cert /etc/ssl/ca1.crt -keyfile /etc/ssl/private/ca1.key
```

Return the resulting certificate, "ca2.crt," to **ca2**.

![Alt text](pic/Picture41.png?raw=true "Title")

-------

Second, move local CA's CSR to the intermediate CA, that is, transfer "ca3.csr" from **ca3** to **ca2**. 

![Alt text](pic/Picture39.png?raw=true "Title")

Sign "ca3.csr" with the following command

```
openssl ca -extensions v3_ca -in ca3.csr -config /etc/ssl/openssl.cnf -days 3000 -out ca3.crt -cert ca2.crt -keyfile /etc/ssl/private/ca2.key
```

Return the resulting certificate, "ca3.crt," to **ca3**.

![Alt text](pic/Picture42.png?raw=true "Title")

-------

Third, move web server's CSR to the local CA, that is, transfer "ws.csr" from **ws** to **ca3**. 

![Alt text](pic/Picture39.png?raw=true "Title")

Sign "ws.csr" with the following command

```
openssl ca -extensions v3_ca -in ws.csr -config /etc/ssl/openssl.cnf -days 3000 -out ws.crt -cert ca3.crt -keyfile /etc/ssl/private/ca3.key
```

Return the resulting certificate, "ws.crt," to **ws**.

![Alt text](pic/Picture42.png?raw=true "Title")


To enable **ws**’s certificate, the certificate and private key should be put into "/etc/ssl."

```
sudo cp <ws_cert_location> /etc/ssl/certs
sudo cp <ws_key_location> /etc/ssl/private
```

For example,

```
sudo cp ~/ws.crt /etc/ssl/certs/
sudo cp /etc/ssl/ws.key /etc/ssl/private/
```

## Server setups

This part is pretty much the same as the previous lab, for simplicity, detail explainations for installations are omitted and only commands are listed here.


###### Install LAMP

```
sudo apt-get update
sudo apt-get install mysql-server
sudo apt-get install apache2
sudo apt install php-pear php-fpm php-dev php-zip php-curl php-xmlrpc php-gd php-mysql php-mbstring php-xml libapache2-mod-php
sudo apt-get install php-intl php-imagick php-imap php-mcrypt php-memcache php7.0-ps php-pspell php-recode php-snmp php7.0-sqlite php-tidy php7.0-xsl
```

###### Write a simple PHP website

First change the permission of "/var/www."

```
sudo chmod 777 /var/www
```

Second, create a file named “info.php” under the “/var/www/html” folder, then copy these codes into this file:

```
<?php
phpinfo();
?>
```

Third, restart the Apache service.

```
sudo /etc/init.d/apache2 restart
```

###### Enable Apache SSL connection

To enable SSL connection, copy "default-ssl.conf" from "sites-available" to "sites-enabled" directory.

```
sudo cp /etc/apache2/sites-available/default-ssl.conf  /etc/apache2/sites-enabled/default-ssl.conf
```

Then, use 

```
ifconfig
```

to find IP address, which is needed when modifying the "default-ssl.conf" in "sites-enabled":

```
<IfModule mod_ssl.c>
        <VirtualHost 172.17.2.18:443>
                ServerAdmin webmaster@localhost
                ServerName www.jhuws.edu
                DocumentRoot /var/www/html

                # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
                # error, crit, alert, emerg.
                # It is also possible to configure the loglevel for particular
                # modules, e.g.
                #LogLevel info ssl:warn

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                # For most configuration files from conf-available/, which are
                # enabled or disabled at a global level, it is possible to
                # include a line for only one particular virtual host. For example the
                # following line enables the CGI configuration for this host only
                # after it has been globally disabled with "a2disconf".
                #Include conf-available/serve-cgi-bin.conf

                #   SSL Engine Switch:
                #   Enable/Disable SSL for this virtual host.
                SSLEngine on

                #   A self-signed (snakeoil) certificate can be created by installing
                #   the ssl-cert package. See
                #   /usr/share/doc/apache2/README.Debian.gz for more info.
                #   If both key and certificate are stored in the same file, only the
                #   SSLCertificateFile directive is needed.
                SSLCertificateFile      /etc/ssl/certs/jhuws.crt
                SSLCertificateKeyFile /etc/ssl/private/jhuws.key
```

In line 2, replace "\_default\_" with the IP address you just found. Do not remove “:443” after the IP addres. It is the port number for SSL connection. In line 4, insert a line and specify your ServerName. Note that this should be consistent with your previous setting while generating CSR. In line 32 and 33, change the directory to where your certificate and private key are stored. Save all these changes and use

```
sudo a2enmod ssl
```

to enable the SSL module of Apache2. Then, restart apache2 server using command:

```
sudo service apache2 restart
```

## Certificate Issuance Test

Now you can test the result of all the previous work by connecting web server from the **user** node. First, ssh the **user** node and install firefox. 

```
sudo apt-get install firefox
sudo apt-get install libcanberra-gtk3-module
sudo apt-get install dbus-x11
```

Launch firefox and import all CA's certificates. 


Type "https://jhuws.edu" into your search bar and see if you can get a green lock.

## Assignment

1. Take a look at the news of an information security company, DigiNotar. It was a root certificate authority. An attacker stole its private key and maliciously issueed digital certificate for others. The question is what file in our experiment does the stolen key corresponds to? 

News page: http://www.slate.com/articles/technology/future_tense/2016/12/how_the_2011_hack_of_diginotar_changed_the_internet_s_infrastructure.html

2. In our experiment, which node performs the role of root CA? What will happen if the root CA revokes the digital certificate of the intermediate CA? Try this and screenshot your result.


# Lab1 Issue and Revoke Certificates

- Introduction
  - LAMP
  - Certificate Authority
- Implementation
  - Network topology on GENI
  - Setup LAMP on GENI
    - Install MySQL
    - Install Apache
    - Install PHP
    - Install the extension packets of PHP and MySQL
    - Enable SSL connection of Apache
  - Install and enable browser on GENI
    - For Windows operation system:
    - For MacOS operation system
    - For other Linux operation system
    - Test Apache service and PHP service
      - Test Apache service
      - Test PHP service
  - Set up certificate authority on GENI
    - Build Certificate Authority
    - On web server node
  - Issue a digital certificate
  - Result of issued digital certificate
  - Revoke a digital certificate
  - Result of revoke a digital certificate


# Introduction

## LAMP

LAMP is short for the software bundle of Linux operating system, Apache HTTP Server, MySQL database management system and PHP programming language.
This bundle can realize the role and function of a web server, which can drive Web applications. Although not actually designed to work together, these open source software is comparatively simple and easy to use.
Besides this four software, this software bundle can also be combined with many other free and open-source software packages

## Certificate Authority
Certificate Authority is a trusted third party that issues electronic documents that verify a digital entity’s identification on the Internet. 
In cryptography mean, certificate authority verifies the ownership of the public key of the named subject of the certificate.

# Implementation

## Network topology on GENI

![Alt text](pic/Picture1.png?raw=true "Title")


The node named CA will be the certificate authority in this experiment. The node named WS will be the web server in this experiment and we will install LAMP on this node to enable it to be a web server. Notice: we must wait for all the GENI node turn green, which means the remote machines are ready for us to use, then we can continue the following steps. This may take a while.

## Setup LAMP on GENI
We already have the GENI node. In other words, we already have a Linux operation system, so we only need to install the remaining Apache, MySQL, and PHP. We should pay attention to the installation order of LAMP, I recommend we install the MySQL and Apache firstly, leave the PHP in the end. The order of installation of MySQL and Apache can be reversed because they are not depending on each other. However, the PHP must be installed after we finish the installation of MySQL and Apache because PHP server depends on the services of Apache and MySQL.
Using SSH log onto the WS node. The following installation will be on this node.
Before installation, we should download the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.
Command: “sudo apt-get update”

###### Install MySQL:
```
sudo apt-get install mysql-server
```

![Alt text](pic/Picture2.png?raw=true "Title")



In this process, it will ask you to enter the password for the MySQL administrator, set up the password in this prompt window.

![Alt text](pic/Picture3.png?raw=true "Title")


After installation of MySQL, we should check whether this is installed successfully.

![Alt text](pic/Picture4.png?raw=true "Title")


```
sudo netstat -tap | grep mysql
```

If it shows the listening port of MySQL as following, then we prove it is installed successfully.
 
###### Install Apache

```
sudo apt-get install apache2
```

![Alt text](pic/Picture5.png?raw=true "Title")

We can run the browser to check whether it is installed successfully or not. But don’t hurry; we need to involve third party software to enable the graphics showing on GENI node. We will cover this content in the following.

###### Install PHP

```
sudo apt-get install php5 libapache2-mod-php5
```

![Alt text](pic/Picture6.png?raw=true "Title")


After this installation, it will create a folder named “www” under var. This folder will be reserve the source code of the website.
 
![Alt text](pic/Picture7.png?raw=true "Title")


###### Install extension packages of PHP and MySQL

```
Command:” sudo apt-get install php-mysql php-curl php-gd php-intl php-pear php-imagick php-imap php5-mcrypt php5-memcache php5-ming php5-ps php5-pspell php5-recode php5-snmp php5-sqlite php5-tidy php5-xmlrpc php5-xsl
```



Notice: the GENI node is initiated with an Ubuntu operation system following its default setting. If you choose other operation systems rather the default operation system, there might be a warning message showing on the screen and it may be failed to initiate the GENI nodes. Therefore there is no need to set up another operation system, but if you do so, either it will not affect much in this experiment. 

###### Enable SSL connection of Apache

Because we need to install the digital certificate later, so we need to enable SSL connection of Apache.
First, just a brief introduction of Apache configuration file.
 
 
![Alt text](pic/Picture8.png?raw=true "Title")

As we can see, there are several configurations in Apache folder.
In the old versions of Apache, there is only one configuration named “httpd.conf”.
And as for the latest version, the main configuration file is “apache2.conf”. We can take a quick look of this file.

![Alt text](pic/Picture9.png?raw=true "Title")


As we can see, there are many “includes” command in this file. It means the apache server will firstly read this file and the other configuration files will be linked using these “include” command.

As for the SSL configuration file, it’s in the “sites-enabled” folder.
We can use this command to create a configuration file for SSL connection.

```
sudo cp /etc/apache2/sites-available/default-ssl.conf  /etc/apache2/sites-enabled/default-ssl.conf
```

Then we modify this default-ssl.conf like follows:

![Alt text](pic/Picture10.png?raw=true "Title")


We temporary named our server name as jhuws.edu, and the digital certificate name is jhuws.crt, the private key of digital certificate name is jhuws.key. Of course you can make the name as you like, but make sure to use the same name in the later.
And the 172.17.2.41 is the IP address of our web server, you can use command “ifconfig” to check it out. The “443” is the port number for SSL connection.
You should also use this command

```
sudo a2enmod ssl
```

to enable the SSL module of Apache2 if there prompt the problem that you try to connect 443 port to our web server while it refuse.
Then restart the apache2 service using this command”service apache2 restart”.

## Install and enable browser on USER node

We choose Firefox browser in this experiment. Of course, you can choose other browsers if you like. This part should be done on user node.

```
sudo apt-get install firefox
```

![Alt text](pic/Picture11.png?raw=true "Title")


The operation of next step will be different for windows, Mac and Linux operation systems. For windows and MacOS operation systems, we need to depend on third party software to enable the graphics display on GENI node.

###### For Windows operation system:
Install the Xming software on your local operation system. Xming is an X11 display server for Microsoft Windows operating systems. Then run it to start the X server. You should see the Xming icon in the taskbar if it is running.

![Alt text](pic/Picture12.png?raw=true "Title")


Then use PuTTY to log onto the GENI node. 
You can see the instruction here about how to log onto GENI node using PuTTY.
http://groups.geni.net/geni/wiki/HowTo/LoginToNodes
Remember to click the option on X11 option besides the other steps of logging onto GENI node using PuTTY.


![Alt text](pic/Picture13.png?raw=true "Title")


Then we can run the graphics display on GENI node on Windows operation system.

```
firefox
```
![Alt text](pic/Picture14.png?raw=true "Title")


Wait for a second, and then we can see the browser GUI display is shown with the help of Xming

![Alt text](pic/Picture15.png?raw=true "Title")



###### For MacOS operation system

Install XQuartz on your Mac. XQuartz is an X server designed for MacOS.
 
![Alt text](pic/Picture16.png?raw=true "Title")

 
Right click on the XQuartz icon in the dock and select Applications > Terminal. This should bring up a new xterm terminal windows.

![Alt text](pic/Picture17.png?raw=true "Title")

Then make an ssh connection to the GENI node on this terminal windows.

![Alt text](pic/Picture18.png?raw=true "Title")

 
We can enable the graphics display of browser on GENI node with the help of XQuartz software.

```
firefox
```

![Alt text](pic/Picture19.png?raw=true "Title")
 
###### For other Linux operation system
It’s much simple when you are using Linux operation system.
Just ssh into the Linux system of your choice using the -Y argument.

![Alt text](pic/Picture20.png?raw=true "Title")


Then run Firefox browser.
```
firefox
```
 
![Alt text](pic/Picture21.png?raw=true "Title")

## Test Apache service and PHP service

Now because we already installed and enable the browser. We can test the Apache service and PHP service installed before.
###### Test Apache service

Open the Firefox browser using command 

```
firefox
```

Enter “127.0.0.1” on the browser. If it shows the following image then it means the Apache service is installed successfully.

![Alt text](pic/Picture22.png?raw=true "Title")

###### Test PHP service

We can write a simple PHP website and then run it to test whether the PHP service is installed successfully or not.
For the convenience, we can use the WinSCP software to write PHP source code. The instruction of how to log onto GENI node using WinSCP can be found in this link:
http://mountrouidoux.people.cofc.edu/CyberPaths/winscp.html
 
![Alt text](pic/Picture23.png?raw=true "Title")


We need to give the privilege to edit the "www" folder under the /var. As I mentioned before, "www" folder contains the website source code.
```
sudo chmod 777 /var/www
```

![Alt text](pic/Picture24.png?raw=true "Title")



Under the “www/html”folder, create a file named “info.php” and then write the following code into it:

```
<?php
phpinfo();
?>
```
![Alt text](pic/Picture25.png?raw=true "Title")


And then we need to restart the Apache service.
```
sudo /etc/init.d/apache2 restart
```
![Alt text](pic/Picture26.png?raw=true "Title")
 

Run the browser and this time enters "127.0.0.1/info.php" into the browser. If we can see the relative configuration information of PHP then it means that PHP service is installed successfully.

![Alt text](pic/Picture27.png?raw=true "Title")


## Set up certificate authority on GENI

OpenSSL is a cryptographic toolkit for SSL and TLS. In this lab, it will be used to:

(1) generate private key and self-signed certificate for certificate authority,
(2) generate private key and certificate signing request for web server,
(3) sign the certificate signing request generated by the web server.

Fortunately, this toolkit is pre-installed on GENI, thus no installation is needed. After connecting to the CA node, take a look at the "/etc/ssl" directory. This is the directory that stores all files related to Openssl.

```
cd /etc/ssl
ls
```
You will see three files under this directory.

```
/etc/ssl
    certs
    openssl.cnf
    private
```

“certs” stores the digital certificate of this machine. “private” stores private keys. “openssl.cnf” specifies the configuration of OpenSSL.


###### Build Certificate Authority
Before this node can function as a certificate authority, some preparations need to be done. First, "index.txt", "newcerts", and "serial" should be created in this directory.

```
/etc/ssl
    certs
    index.txt
    newcerts
    openssl.cnf
    private
    serial
```

Since all these changes require root privilage, it is suggested to get into root environment before you start:

```
sudo su
touch index.txt
mkdir newcerts
echo 01 > serial
```
 
Second, "openssl.cnf" should be edited as follows. You can either edit it remotely through "vim"/"vi" or transfer the file to your computer and edit it with your own editors. If you're doing this in the first way, use ":42" to get to line 42, and replace the directory ".demoCA" with "/etc/ssl."

```
 [ CA_default ]
 
 dir             = /etc/ssl              # Where everything is kept
 certs           = $dir/certs            # Where the issued certs are kept
 crl_dir         = $dir/crl              # Where the issued crl are kept
 database        = $dir/index.txt        # database index file.
 #unique_subject = no                    # Set to 'no' to allow creation of
```

[optional] Go to line 129, 134, 139 and 146 to change the defaut values.

```
[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = US
countryName_min                 = 2
countryName_max                 = 2

stateOrProvinceName             = State or Province Name (full name)
stateOrProvinceName_default     = MD

localityName                    = Locality Name (eg, city)

0.organizationName              = Organization Name (eg, company)
0.organizationName_default      = JHU

# we can do this but it is not needed normally :-)
#1.organizationName             = Second Organization Name (eg, company)
#1.organizationName_default     = World Wide Web Pty Ltd

organizationalUnitName          = Organizational Unit Name (eg, section)
organizationalUnitName_default  = ISI
```

If you choose to do download the file, here are some tips for file transfer. 

(1) SFTP

First, establish a sftp connection by:

```
sftp -i <geni_private_key> -oPort=<geni_port> <user>@<host>
```

For example, 

```
sftp -i ~/.ssh/id_geni_ssh_rsa -oPort=28693 yjou2@pc2.geni.it.cornell.edu
```

After putting in the passphrase created when generating private key, a sftp> prompt should appear. Now, use "mget" command to fetch the file. If the file is retrieved succefully, it will show you the file name, file size, transfer rate and elapsed time.

```
sftp> mget /etc/ssl/openssl.cnf
Fetching /etc/ssl/openssl.cnf to openssl.cnf
/etc/ssl/openssl.cnf                                            100%   11KB 213.1KB/s   00:00  
```

Once finishing editing files, use "put" command to upload thems.

```
sftp> put openssl.cnf
Uploading openssl.cnf to /users/yjou2/openssl.cnf
openssl.cnf                                                     100%   11KB 147.7KB/s   00:00   
```

(2) WinSCP

This method requires you to change the permission of "openssl.cnf" first.

```
sudo chmod 777 openssl.cnf
```

Then, edit it directly.

![Alt text](pic/Picture32.png?raw=true "Title")


![Alt text](pic/Picture33.png?raw=true "Title")

 
Now, the CA node can generate its private key and self-signed certificate. The command of generating private key is:

```
openssl genrsa -out private/cakey.pem 2048
```

You can check whether the private key is generated succefully by listing files in "private." If everything went well, you will see a "cakey.pem" in it.

```
ls private
```

Next, the command of creating self-signed certificate is:

```
openssl req -new -x509 -key private/cakey.pem -out cacert.pem
```

It will ask you to type in information such as country name and organizaton name. If you did modify default values in previous steps, just leave it blank if values inside those brackets are the same as your expectation. Note that no server will be setup on CA node, so you can set whatever common name you want. However, when it comes to WS node, the common name should be consistent with your configuration.

```
root@ca:/etc/ssl# openssl req -new -x509 -key private/cakey.pem -out cacert.pem
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [US]:
State or Province Name (full name) [MD]:
Locality Name (eg, city) []:Baltimore
Organization Name (eg, company) [JHU]:
Organizational Unit Name (eg, section) [ISI]:
Common Name (e.g. server FQDN or YOUR name) []:jhuca.edu            
Email Address []:
```

###### Generating certificate signing request

Now, the CA node is ready to sign WS's certificate. The next step is to connect to the WS node and generate a certificate signing request(CSR). The following commands are used to generate private key and corresponding CSR.

```
cd /etc/ssl
sudo su
openssl genrsa -out jhuws.key 2048
openssl req -new -key jhuws.key -out jhuws.csr
```
 
Similar to the process of generating self-signed certificate on the CA node, it will ask you to put in several information. However, the common name cannot be set arbitrarily this time, it should be consistent with your configuration.

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
Common Name (e.g. server FQDN or YOUR name) []:jhuws.edu
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

###### Transfer certificate signing request

Still, there are several ways to transfer files.

(1) SFTP

First, setup a connection with WS node and download the CSR.

```
sftp -i <geni_private_key> -oPort=<geni_port> <user>@<host>
mget <file_directory>
```

For example, 

```
$ sftp -i ~/.ssh/id_geni_ssh_rsa -oPort=28693 yjou2@pc2.geni.it.cornell.edu
Enter passphrase for key '/Users/yu-tsern/.ssh/id_geni_ssh_rsa': 
Connected to pc2.geni.it.cornell.edu.
sftp> mget /etc/ssl/jhuws.csr
Fetching /etc/ssl/jhuws.csr to jhuws.csr
/etc/ssl/jhuws.csr                                              100%  993    18.9KB/s   00:00    
sftp> 
```

Then, "ctrl + D" to exit the connection and setup another connection with the CA node. Note that we are not permitted to write to the "etc/ssl" on CA directly, thus it is put in "~" instead. It does not matter where we put the CSR file since the file location can be specified when signing CSRs.

```
$ sftp -i ~/.ssh/id_geni_ssh_rsa -oPort=28691 yjou2@pc2.geni.it.cornell.edu
Enter passphrase for key '/Users/yu-tsern/.ssh/id_geni_ssh_rsa': 
Connected to pc2.geni.it.cornell.edu.
sftp> put jhuws.csr
Uploading jhuws.csr to /users/yjou2/jhuws.csr
jhuws.csr                                                       100%  993    36.1KB/s   00:00  
```

(2) WinSCP

Using WinSCP requires changing the permission of "openssl.cnf."

```
chmod 777 <file_name>
```

If you're using the same file name as we did in the previous steps, it is ”chmod 777 jhuws.csr”

![Alt text](pic/Picture37.png?raw=true "Title")

![Alt text](pic/Picture38.png?raw=true "Title") 


###### Sign a CSR

After CSR is transfered to the CA node, we can sign it and generate a certificate.

```
sudo openssl ca -in <csr_location> -out <certificate_location> -days 3650
```

If you used sftp to transfer CSR, it is probably located at "~", thus, the command should look like:

```
sudo openssl ca -in ~/jhuws.csr -out ~/jhuws.crt -days 3650
```

It will ask you whether you would like to sign this request. Type "y" to those questions.

```
Using configuration from /usr/lib/ssl/openssl.cnf
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: Jun  9 22:50:08 2018 GMT
            Not After : Jun  6 22:50:08 2028 GMT
        Subject:
            countryName               = US
            stateOrProvinceName       = MD
            organizationName          = JHU
            organizationalUnitName    = ISI
            commonName                = jhuws.edu
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                A6:40:9B:DC:99:67:27:C6:23:D0:72:A6:C6:FD:A9:FD:17:A9:45:E2
            X509v3 Authority Key Identifier: 
                keyid:8B:D6:E6:4D:D7:28:3D:02:5E:35:12:7F:A0:82:B6:4F:9C:AF:F2:68

Certificate is to be certified until Jun  6 22:50:08 2028 GMT (3650 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
```

Once WS's certificate is generated, transfer "jhuws.crt" to WS node and "cacert.pem" to USER node. In general, a chain of certificates are needed when the server is proving its ownership of the public key. However, for simplicity, there's only one certificate in the chain in this lab. Additionally, recall that when client sees a chain of certificates, it trace the path of trust back to the root CA and see if it is trust worthy, and that is the reason why "cacert.pem" should be transfered to the USER node. 

If you transfer the certificate through your computer, either by SFTP or WinSCP, you can open it and take a look at its content. Use "cat" command to view it on Linux, or double click it on Mac/Windows. You can see that "jhuws.crt" is issued by "jhuca.edu," which is the certificate authority. 

To enable WS’s certificate, the certificate and private key should be put into "/etc/ssl."

```
sudo cp jhuws.crt /etc/ssl/certs
sudo cp jhuws.key /etc/ssl/private
```


## Result of issued digital certificate
Now we can view the result of the former work we have done.
The following operations are all on user node.
Firstly log onto the user node.
 
Because the display of browser on GENI is kind of slow. TO quick test whether we are right by far, we can install the “curl” to perform a quick test.
Curl is a tool to transfer data from or to a server, using one of the supported protocols.
Use 
```
sudo su
``` 
to enter the root account
Use 
```
apt-get update
```
to update the existed packets.
```
apt-get install curl
```
to install the curl.

Then move to the hosts file to do a little modification.

![Alt text](pic/Picture46.png?raw=true "Title")


Use command 
```
chmod 777 hosts
```
to give out the privilege.


![Alt text](pic/Picture47.png?raw=true "Title")
 
We do these modifications on it. While the “172.17.2.41” is the IP address of ws node, this is the web server in out topology. And jhuws.edu is the server name we set up by ourselves before.

Use command 
```
curl jhuws.edu
```
to see if it works right.

![Alt text](pic/Picture48.png?raw=true "Title")

 
It returns the HTML response of our web server. It seems it works well.
Then we test whether SSL configuration we modified before is work right.
We use this command 
```
curl https://jhuws.edu --cacert cacert.crt
```
to test.

![Alt text](pic/Picture49.png?raw=true "Title")
 
It also works well. So we can turn to the browser.
Open the browser on user node. You can refer the instruction before about how to run and open a browser on GENI node.
We visit the jhuws.edu/info.php first.
 
![Alt text](pic/Picture50.png?raw=true "Title") 


It works right, and then we install the digital certificate. We go to the preference option of the browser.

![Alt text](pic/Picture51.png?raw=true "Title")

 
Then go to the Advanced, Certificates options. Click on “View Certificates”

![Alt text](pic/Picture52.png?raw=true "Title")


Then import the cacert.crt file we put on user node before.

![Alt text](pic/Picture53.png?raw=true "Title")


Trust this certificate authority we build by our own.

![Alt text](pic/Picture54.png?raw=true "Title")


Now we can see our certificate authority is in the list of certificate authorities.
 
![Alt text](pic/Picture55.png?raw=true "Title")


We visit jhuws.edu/info.php firstly, we can see the connection is unsecure.

![Alt text](pic/Picture56.png?raw=true "Title")

 
Then we visit https://jhuws.edu/info.php

![Alt text](pic/Picture57.png?raw=true "Title")


And this time we can see there appears a green lock and remind us now it’s a secure connection.
We can view the details of this connection by click the “>”.

![Alt text](pic/Picture58.png?raw=true "Title")


The information we put in when making the digital certificate is shown in this display.
By now, we have already finished the experiment of building a certificate authority and issue a digital certificate.

## Revoke a digital certificate
Then we can try to revoke the digital certificate to see how it works and what is the result it will turn out.
To revoke the digital certificate we issue before, we should log onto the ca node, which is the certificate authority.
Use this command to revoke the digital certificate we issued before. The name of digital certificate we issued before is “jhuws.crt”
```
openssl ca -revoke jhuws.crt
```

![Alt text](pic/Picture59.png?raw=true "Title")

We can use this command”cat index.txt” to check the index of our digital certificate. It’s 03, so everything is going right.

![Alt text](pic/Picture60.png?raw=true "Title")


And we should renew the crl, which is short for Certificate Revocation List. The digital certificate we revoked will be given the index and record in this list.
Use this command:
```
openssl ca -gencrl -out thisca.crl
```
to generate the index of the digital certificate we just revoke.
We can have a look at the thisca.crl file.

![Alt text](pic/Picture61.png?raw=true "Title")

 
It stores the Certificate Revocation List.
Then we need add content ”00” into ”crlnumber”, to let the crl start to record.
Each time we revoke a digital certificate, it will add one to itself.

![Alt text](pic/Picture62.png?raw=true "Title")

 
I have revoked twice the digital certificate, so the number shown is 02.
Then we have successfully revoked a digital certificate we issued before.


## Connect after certificate is revoked

To see the result after we revoke an issued digital certificate. Because we are actually not using network to verify. Therefore we need to import the new cacert.crt onto the user node.
Remove the old cacert.crt and import the new cacert.crt. As the same we have done before, we actually move the file “cacert.pem”, but we need to change the extension”.pem” to “.crt”.
 
 ![Alt text](pic/Picture63.png?raw=true "Title")
 
 ![Alt text](pic/Picture64.png?raw=true "Title")
 
 
Also we should move the thisca.crl which stores the Certificate Revocation List to the user node.

 ![Alt text](pic/Picture65.png?raw=true "Title")
 
Because our certificate authority is offline, therefore we need to move these files manually.
Then we can see the result after we revoke a digital certificate. We still use curl tool to find out.
We use this command:
```
curl https://jhuws.edu --cacert cacert.crt --crlfile thisca.crl
```
to visit our site we build before. We need the options”—cacert” and “—crlfile” to include the ca document and crl document manually.

 ![Alt text](pic/Picture66.png?raw=true "Title")

 
Now we can see the digital certificate has already been revoked.
As for the Firefox browser we installed before, we also need to update the ca document and crl document. But Firefox browser has already remove the user interface on Firefox to import the crl document, so we can’t see the result on Firefox since we are the offline certificate authority and need us to import the ca document and crl document manually.

https://wiki.mozilla.org/CA:ImprovingRevocation#Preload_Revocations_of_Intermediate_CA_Certificates

 ![Alt text](pic/Picture67.png?raw=true "Title")
 

Revise
You also can try the tool named MobaXterm to log in GENI node and move the file.

 ![Alt text](pic/Picture68.png?raw=true "Title")

As you can see from the above graph, on the left part of this tool you can move the file and the right part of this tool is the terminal. It would be more convenient for your experiment.
Assignment
1 Google announces to mark the sites without HTTPS in chrome as non-secure by the end of January 2017, which means HTTPS is now mandatory for secure data in chrome. What’s the reason for Google to do so?
2 Find out one of the digital certificate stored on your laptop. Screenshot the digital certificate you found on your laptop.
3 X.509 is a popular SSL digital certificate standard, what content is included in the digital certificate in X.509 standard? Check the content in our digital certificate, screen shot it.


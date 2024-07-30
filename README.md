# webinar-webgateway
Documents And Demos of the Web gateway Webinar



| Directorio                                                   | Contenido                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| .\doc                                                        | Powerpoint Presentation                                      |
| .                                                            | Step by Step Demos and samples                               |
| https://github.com/intersystems-community/webgateway-examples.git | More official WebGateway Container Samples  from InterSystems |



# Demo1 -  Windows NoPWS Installation

This Demo show how to install the Web Gateway on a Windows Server. 

## Preparation:

A Windows Server gets Provisioned, and IRIS 2024 gets installed.

## Demo

* Show Web Server Role, IIS Already Installed
* Show Default Web Site.
* Install IRIS 
  * Explain Options
  * Explain Password 
* Access Portal -> Fail.  http://localhost/iris/csp/sys/UtilHome.csp
* Resolve issue:  http://localhost/iris/csp/bin/Systems/Module.cxw
  * Change password to Connect to IRIS
* Show Windows Firewall and Configure.



# Demo2 - Apache Connecting to Remote Server

For this Demo, an Ubuntu VM gets provisionned.

### Copy IRIS files

``` 
 scp -r -i ESPYD-London.pem /mnt/c/webinar/Webinar-Webgateway/SSLTLS/ ubuntu@13.40.103.158:/home/ubuntu
 scp -r -i ESPYD-London.pem /mnt/c/webinar/Webinar-Webgateway/DistribNoPWS/Ubuntu2204/ u
buntu@13.40.103.158:/home/ubuntu
scp -r -i ESPYD-London.pem /mnt/c/webinar/Webinar-Webgateway/DistribNoPWS/Licenses/ ubu
ntu@13.40.103.158:/home/ubuntu
```

### Prepare server

```
sudo apt update -y
sudo apt upgrade -y
sudo apt install snap
```



## Demo

### Install Apache:

```
sudo apt install apache2
sudo systemctl enable apache2
systemctl start apache2
```

* Check Access from Web Browser. Should get Ubuntu Index Page.



### Enable SSL/TLS on Server

usando certbot de letsencrypt

```
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Runing certbot

```
sudo /snap/bin/certbot –apache
```

ServerName: dryrun.iris-datafabric.es



* Check Https Access to dryrun.iris-datafabric.es

  

### Install Web gateway

```
cd /home/ubuntu/Ubuntu2204/webGateway-2023-2-0.204.0lnxubuntu2204x64
cd ./install
sudo ./GatewayInstall
sudo systemctl restart apache2
```

* Accept all install defaults.

* Now Validate Access to Server:

  ```
  https://dryrun.iris-datafabric.es/csp/bin/Systems/Module.cxw
  ```

  * Allow remote access for now.

* Access Portal

  ```
  https:////dryrun.iris-datafabric.es/csp/sys/UtilHome.csp
  ```

  * Correct the Web Application to Forward All Files to IRIS
    * Edit /etc/apache2/apache2.conf
      * CSP On
  * Restart Apache

# Demo3 Enable SSL /TLS to Server

### Setup SSL/TLS with Server

#### In windows:

* Setup %SuperServer SSL Configuration
* System Wide Security Parameters: 
  * SuperServer SSL/TLS support: Required

#### In Linux:

* Copy the ssl/tls to /opt/webgateway dir

```
cd 
sudo cp -r SSLTLS/ /opt/webgateway/
```

Edit  the Configuration

```
https://dryrun.iris-datafabric.es/csp/bin/Systems/Module.cxw	
```

Server Access parameters for "IRIS"

| Connection Security Level   | SSL/TLS                           |
| --------------------------- | --------------------------------- |
| SSL/TLS Certificate File    | /opt/webgateway/SSLTLS/w1.crt     |
| SSL/TLS Private Key File    | /opt/webgateway/SSLTLS/w1.key     |
| SSL/TLS CA Certificate File | /opt/webgateway/SSLTLS/rootCA.crt |



# Demo4: Web Gateway Component



## Preparation

### Install Docker

Uninstall conflicting packages

```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Istall using convience script

```
curl -fsSL https://get.docker.com -o get-docker.sh
 sudo sh get-docker.sh
```

Configure

```
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Logout/Login, and test

```
docker run hello-world
```

### Copy Files to Server

````
scp -r -i ESPYD-London.pem /mnt/c/webinar/Webinar-Webgateway/DistribNoPWS/Container  ubuntu@13.40.103.158:/home/ubuntu
````

Uncompress and  Load

```
cd Container
docker load -i webgateway-2023.1.0.235.1-docker.tar.gz
```

Loaded image: intersystems/webgateway:2023.1.0.235.1

Sample Docker-compose

````

````



## Run Container

````
docker compose up
````

Edit the config files

```
http://dryrun.iris-datafabric.es:8080/csp/bin/Systems/Module.cxw
```



# WebGateway Container samples

URL: https://github.com/intersystems-community/webgateway-examples.git

Running

```
export IRISTAG=containers.intersystems.com/intersystems/iris:2022.1.0.209.0
export WEBTAG=intersystems/webgateway:2023.1.0.235.1
```







## Notes On WebGateway Container:

- The **webgateway** image contains the following components, installed by root in the indicated locations within the container:

  - An InterSystems Web Gateway in **/opt/webgateway**.
  - An Apache web server in **/etc/apache2**.

  

````
For testing purposes, all three webgateway images contain basic versions of the CSP.ini file (which contains the Web Gateway configuration) and the CSP.conf file (which contains the Web Gateway’s Apache or Nginx-specific configuration), in the directories indicated in the preceding list. However, you can provide your own version of one or both files to overwrite these.
````


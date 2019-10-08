# Install Guacamole on Ubuntu 18.04

Apache Guacamole is a gateway server that allows users to use browser to access Windows and Linux desktop services on 
other/same machines through HTML5.

This document demonstrates an installation of Guacamole 1.0.0 on Ubuntu 18.04.3.

## Install dependencies
Build-essential:

    sudo aptitude install build-essential

Required dependencies:

    sudo aptitude install libcairo2-dev libjpeg-turbo8-dev libpng-dev libossp-uuid-dev
    
Optional dependencies

    sudo aptitude install libavcodec-dev libavutil-dev libswscale-dev
    sudo aptitude install libfreerdp-dev
    sudo aptitude install libpango1.0-dev
    sudo aptitude install libssh2-1-dev
    sudo aptitude install libvncserver-dev
    sudo aptitude install libpulse-dev
    sudo aptitude install libssl-dev
    sudo aptitude install libvorbis-dev
    sudo aptitude install libwebp-dev

Tomcat:
    sudo aptitude install tomcat8 tomcat8-admin tomcat8-common tomcat8-user
    sudo ufw allow 8080

## Installing server - Obtaining source code
    mkdir guacamole
    cd guacamole/
    
Download http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/source/guacamole-server-1.0.0.tar.gz to this directory.

Unpack:

    tar -zxvf guacamole-server-1.0.0.tar.gz
    cd guacamole-server-1.0.0

Config:

    ./configure --with-init-dir=/etc/init.d

Make:

    make
    sudo make install
    sudo ldconfig

Start gaucd, the gaucamole backend.

    sudo systemctl enable guacd
    sudo systemctl start guacd
    
## Installing client - Use binary

Go back to guacamole folder

    cd ..

Download http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/binary/guacamole-1.0.0.war to this
 directory

## Deploying Guacamole front-end

    mkdir /etc/guacamole
    sudo mv guacamole-1.0.0.war /etc/guacamole/guacamole.war
    sudo ln -s /etc/guacamole/guacamole.war /var/lib/tomcat8/webapps/
    sudo systemctl restart tomcat8
    sudo systemctl restart guacd

By now, pointing browser to http://localhost:8080/guacamole should give you a login screen. Next we need to 
configure Guacamole.

## Configuring Guacamole
    
    sudo mkdir /etc/guacamole/{extensions,lib}
    sudo -i
    echo "GUACAMOLE_HOME=/etc/guacamole" >> /etc/default/tomcat8
    exit


The configuration file is located at /etc/guacamole/guacamole.properties.
``` /etc/guacamole/guacamole.properties
# Hostname and port of guacamole proxy
guacd-hostname: localhost
guacd-port:     4822
user-mapping:    /etc/guacamole/user-mapping.xml
auth-provider:    net.sourceforge.guacamole.net.basic.BasicFileAuthenticationProvider
```

Save the configuration to Tomcat servlet:
    
    ln -s /etc/guacamole /usr/share/tomcat8/.guacamol

## Setting default authentication
This sample file /etc/guacamole/user-mapping.xml uses plain password.


```
<user-mapping>
	
    <!-- Per-user authentication and config information -->
    <authorize username="USERNAME" password="PASSWORD">

        <!-- First authorized connection -->
        <connection name="localhost">
            <protocol>vnc</protocol>
            <param name="hostname">localhost</param>
            <param name="port">5901</param>
            <param name="password">VNCPASS</param>
        </connection>

        <!-- Second authorized connection -->
        <connection name="otherhost">
            <protocol>vnc</protocol>
            <param name="hostname">otherhost</param>
            <param name="port">5900</param>
            <param name="password">VNCPASS</param>
        </connection>

    </authorize>

</user-mapping>
```
Since this file contains password, secure this password to be read only by root and root group:
```
sudo chmod 640  /etc/guacamole/user-mapping.xml
```


## Change the guacamole url to root
By default, the url is http://localhost:8080/guacamole. If you would like to change this to other form, or even root, you can do this:

This is an example to set root url http://localhost:8080 to guacamole:
```
  cd /var/lib/tomcat8/webapps.
  sudo mv ROOT ROOT.bak
  sudo ln -s guacamole ROOT 
  sudo ln -s guacamole.war ROOT.war
```
   
## Adding client machines

Full documentation can be found at:
https://guacamole.apache.org/doc/gug/configuring-guacamole.html#connection-configuration

Supported protocols are:

1. ssh
2. vnc
3. rdp

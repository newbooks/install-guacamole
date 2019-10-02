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

By now, pointing browser to http://localhost:8080/guacamole should give you a login screen.



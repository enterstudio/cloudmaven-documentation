---
title: Research Computing Case Studies
keywords: research_computing
last_updated: December 19, 2016
tags: [research_computing,geospatial,case_studies]
summary: "Use of Geospatial Tools for Research - Case Studies "
sidebar: mydoc_sidebar
permalink: aws_geoserver.html
folder: mydoc
---

## Installing Geoserver on Ubuntu 14.04 with Tomcat 7
** Note: This is also available as an [Amazon Machine Image (AMI)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) if you don't want to go through the entire installation process - please contact us if you would like to access the AMI.

Spin-up an instance or virtual machine with Ubuntu 16.04 as the operating system. 
  * Make sure to open port 8080 when configuring your security settings during the VM setup

Update the OS and install Tomcat 7

```bash
  $ sudo apt-get update
  $ sudo apt-get upgrade
  $ sudo apt-get install tomcat7
  $sudo apt-get install tomcat7-docs tomcat7-admin tomcat7-examples
  $ sudo apt-get install default-jdk
```

Change permissions for tomcat user roles. Remember to put these in between the <tomcat-users></tomcat-users> tag. 

```bash
$ sudo vi /etc/tomcat7/tomcat-users.xml

<tomcat-users>
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="password" roles="manager-gui,admin-gui"/>
  <user username="tomcat" password="tomcat" roles="tomcat"/>
  <user username="both" password="tomcat" roles="tomcat,role1"/>
  <user username="role1" password="tomcat" roles="role1"/>
</tomcat-users>
```

Increase java heap memory to improve startup speed. Modify the first instance of JAVA_OPTS to read as belo. 

```bash
$sudo vi /etc/default/tomcat7

JAVA_OPTS="-Djava.security.egd=file:/dev/./urandom -Djava.awt.headless=true -Xmx1024m -XX:MaxPermSize=512m -XX:+UseConcMarkSweepGC"

```

Restart Tomcat

```bash
sudo service tomcat7 restart
```

Download the latest release of geoserver web archive on this page: http://geoserver.org/release/stable/
At this time, the latest release is 2.10.

```bash
$ wget 'http://sourceforge.net/projects/geoserver/files/GeoServer/2.10.0/geoserver-2.10.0-war.zip'
$ sudo mv geoserver-2.10.0-war.zip /var/lib/tomcat7/webapp/
$ sudo unzip /var/lib/tomcat7/webapp/
$ sudo service tomcat7 restart
```

That's it. You may be prompted to install unzip in that case, just `sudo apt install unzip` and proceed accordingly. 

The next steps will involve attaching a disk drive to your EC2 instance and specifying the data folder location in geoserver as a best practice to separate your data from your service deployment machine. 
{% include links.html %}

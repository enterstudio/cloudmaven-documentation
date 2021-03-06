---
title: AWS Procedural
keywords: documentation theme, jekyll, technical writers, help authoring tools, hat replacements
last_updated: July 3, 2016
tags: [AWS]
summary: "Jupyter Notebook Troubleshooting on AWS"
sidebar: mydoc_sidebar
permalink: aws_jupyter.html
folder: mydoc
---
## Setting up a public Jupyter Notebook server
This describes how to run a Jupyter Notebook server on an AWS instance for access through a browser on a localhost. Cookbook recipe on:
[http://jupyter-notebook.readthedocs.io/en/latest/public_server.html](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html)

1. Spin up an AWS instance with Ubuntu AMI and install [Anaconda](https://docs.continuum.io/anaconda/install)
2. Install Jupyter Notebook (sudo apt-get install jupyter-notebook)
3. Once you've installed Jupyter Notebook, follow the steps below:

```bash
$ jupyter notebook --generate-config 
```
Then launch ipython and generate a hashed passwor to add to the configuration file you generated:

```bash
$ ipython

In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'
```

Generate a self-signed certificate using openssl so that your hashed password is encrypted

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mykey.key -out mycert.pem
```

Set Jupyter Notebook to use the certificate when it starts: 

```
$ jupyter notebook --certfile=mycert.pem --keyfile mykey.key
```
You can also set the Jupyter Notebook to use the certificate when it starts by editing the configuration file:


```
$ vi ~/.jupyter/jupyter_notebook_config.py
```

You will want to add the following lines to the config file (or uncomment those lines -- remove the hashes): 

```bash
# Set options for certfile, ip, password, and toggle off
# browser auto-opening
c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mykey.key'
# Set ip to '*' to bind on all interfaces (ips) for the public server
c.NotebookApp.ip = '*'
c.NotebookApp.password = u'sha1:bcd259ccf...<your hashed password here>'
c.NotebookApp.open_browser = False

# It is a good idea to set a known, fixed port for server access
c.NotebookApp.port = 9999
```
Note: You will also want to open the port on your AWS Portal (using the Security Groups and Inbound rules)
Note: If http://ipaddress:9999 doesn't work, use https://ipaddress:9999.  

## Creating Alarms for Jupyter Notebook Service
Download PDF [here](/documentation/pdf/Doc43_Jupyter_on_AWS.pdf) 

## Jupyter Notebook As A Service
This document describes how to set your Jupyter Notebook (JpyNb) server to boot on startup so you do not have to type "jupyter notebook" at the command line every time. Also, if your EC2 instance fails and re-boots, JpyNb restarts automatically. 

I'm assuming you have knowledge of basic linux commands e.g. wget, cp, mv and chmod. I also recommend installing JpyNb from Anaconda. The example below assumes that JpyNb is installed through Anaconda. It also assumes that you have an EC2 instance with Ubuntu. 

Download this [init.d script] (https://gist.github.com/Doowon/38910829898a6624ce4ed554f082c4dd) and save as /etc/init.d/jupyter

Edit the file and change this line:

> DAEMON=/home/ubuntu/anaconda3/bin/jupyter-notebook

Also make sure there's a /var/log/jupyter/ folder, or you can create one using mkdir. You may need sudo privileges. 

Then do the following:
```
    sudo chmod +x /etc/init.d/jupyter
    
    # generate a config file
    
    sudo jupyter --generate-config -f /etc/jupyter/jupyter_config.py
    
    # start service
    
    sudo service jupyter start
    
    # start jupyter on boot
    
    sudo update-rc.d jupyter defaults
    
    # stop service
    
    sudo service jupyterhub stop
```

Voila! 


{% include links.html %}

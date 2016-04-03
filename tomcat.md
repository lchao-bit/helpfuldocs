How To Install Apache Tomcat 8 on Ubuntu 14.04
==============================================


### Introduction

Apache Tomcat is a web server and servlet container that is used to serve Java applications. Tomcat is an open source implementation of the Java Servlet and JavaServer Pages technologies, released by the Apache Software Foundation. This tutorial covers the basic installation and some configuration of the latest release of Tomcat 8 on your Ubuntu 14.04 server.

Prerequisites
-------------

Before you begin with this guide, you should have a separate, non-root user account set up on your server. You can learn how to do this by completing steps 1-3 in the [initial server setup](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-14-04) for Ubuntu 14.04. We will be using the `demo` user created here for the rest of this tutorial.

Install Java
------------

Tomcat requires that Java is installed on the server, so any Java web application code can be executed. Let's satisfy that requirement by installing OpenJDK 7 with apt-get.

First, update your apt-get package index:

~~~~ {.code-pre .command}
sudo apt-get update
~~~~

Then install the Java Development Kit package with apt-get:

~~~~ {.code-pre .command}
sudo apt-get install default-jdk
~~~~

Answer `y` at the prompt to continue installing OpenJDK 7.

Now that Java is installed, let's create a `tomcat` user, which will be used to run the Tomcat service.

Create Tomcat User
------------------

For security purposes, Tomcat should be run as an unprivileged user (i.e. not root). We will create a new user and group that will run the Tomcat service.

First, create a new `tomcat` group:

~~~~ {.code-pre .command}
sudo groupadd tomcat
~~~~

Then create a new `tomcat` user. We'll make this user a member of the `tomcat` group, with a home directory of `/opt/tomcat` (where we will install Tomcat), and with a shell of `/bin/false` (so nobody can log into the account):

~~~~ {.code-pre .command}
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
~~~~

Now that our `tomcat` user is set up, let's download and install Tomcat.

Install Tomcat
--------------

The easiest way to install Tomcat 8 at this time is to download the latest binary release then configure it manually.

### Download Tomcat Binary

Find the latest version of Tomcat 8 at the [Tomcat 8 Downloads page](http://tomcat.apache.org/download-80.cgi). At the time of writing, the latest version is **8.0.23**. Under the **Binary Distributions** section, then under the **Core** list, copy the link to the "tar.gz".

Let's download the latest binary distribution to our home directory.

First, change to your home directory:

~~~~ {.code-pre .command}
cd ~
~~~~

Then use `wget` and paste in the link to download the Tomcat 8 archive, like this (your mirror link will probably differ from the example):

~~~~ {.code-pre .command}
wget http://mirror.sdunix.com/apache/tomcat/tomcat-8/v8.0.23/bin/apache-tomcat-8.0.23.tar.gz
~~~~

We're going to install Tomcat to the `/opt/tomcat` directory. Create the directory, then extract the the archive to it with these commands:

~~~~ {.code-pre .command}
sudo mkdir /opt/tomcat
sudo tar xvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
~~~~

Now we're ready to set up the proper user permissions.

### Update Permissions

The `tomcat` user that we set up needs to have the proper access to the Tomcat installation. We'll set that up now.

Change to the Tomcat installation path:

~~~~ {.code-pre .command}
cd /opt/tomcat
~~~~

Then give the `tomcat` user **write** access to the `conf` directory, and **read** access to the files in that directory:

~~~~ {.code-pre .command}
sudo chgrp -R tomcat conf
sudo chmod g+rwx conf
sudo chmod g+r conf/*
~~~~

Then make the `tomcat` user the owner of the `work`, `temp`, and `logs` directories:

~~~~ {.code-pre .command}
sudo chown -R tomcat work/ temp/ logs/
~~~~

Now that the proper permissions are set up, let's set up an Upstart init script.

### Install Upstart Script

Because we want to be able to run Tomcat as a service, we will set up an Upstart script.

Tomcat needs to know where Java was installed. This path is commonly referred to as "JAVA\_HOME". The easiest way to look up that location is by running this command:

~~~~ {.code-pre .command}
sudo update-alternatives --config java
~~~~

~~~~ {.code-pre}
Output:There is only one alternative in link group java (providing /usr/bin/java): /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java
Nothing to configure.
~~~~

The JAVA*HOME will be in the output, without the trailing `/bin/java`. For the example above, the JAVA*HOME is highlighted in red.

Now we're ready to create the Upstart script. Create and open it by running this command:

~~~~ {.code-pre .command}
sudo nano /etc/init/tomcat.conf
~~~~

Paste in the following script, and modify the value of `JAVA_HOME` if necessary. You may also want to modify the memory allocation settings that are specified in `CATALINA_OPTS`:

/etc/init/tomcat.conf

~~~~ {.code-pre}
description "Tomcat Server"

  start on runlevel [2345]
  stop on runlevel [!2345]
  respawn
  respawn limit 10 5

  setuid tomcat
  setgid tomcat

  env JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64/jre
  env CATALINA_HOME=/opt/tomcat

  # Modify these options as needed
  env JAVA_OPTS="-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"
  env CATALINA_OPTS="-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

  exec $CATALINA_HOME/bin/catalina.sh run

  # cleanup temp directory after stop
  post-stop script
    rm -rf $CATALINA_HOME/temp/*
  end script
~~~~

Save and exit. This script tells the server to run the Tomcat service as the `tomcat` user, with the settings specified. It also enables Tomcat to run when the server is started.

Now let's reload the Upstart configuration, so we can use our new Tomcat script:

~~~~ {.code-pre .command}
sudo initctl reload-configuration
~~~~

Tomcat is ready to be run. Start it with this command:

~~~~ {.code-pre .command}
sudo initctl start tomcat
~~~~

Tomcat is not completely set up yet, but you can access the default splash page by going to your domain or IP address followed by `:8080` in a web browser:

~~~~ {.code-pre}
Open in web browser:http://server_IP_address:8080
~~~~

You will see the default Tomcat splash page, in addition to other information. Now we will go deeper into the installation of Tomcat.

Configure Tomcat Web Management Interface
-----------------------------------------

In order to use the manager webapp that comes with Tomcat, we must add a login to our Tomcat server. We will do this by editing the `tomcat-users.xml` file:

~~~~ {.code-pre .command}
sudo nano /opt/tomcat/conf/tomcat-users.xml
~~~~

This file is filled with comments which describe how to configure the file. You may want to delete all the comments between the following two lines, or you may leave them if you want to reference the examples:

tomcat-users.xml excerpt

~~~~ {.code-pre}
<tomcat-users>
...
</tomcat-users>
~~~~

You will want to add a user who can access the `manager-gui` and `admin-gui` (webapps that come with Tomcat). You can do so by defining a user similar to the example below. Be sure to change the username and password to something secure:

tomcat-users.xml — Admin User

~~~~ {.code-pre}
<tomcat-users>
    <user username="admin" password="password" roles="manager-gui,admin-gui"/>
</tomcat-users>
~~~~

Save and quit the tomcat-users.xml file. To put our changes into effect, restart the Tomcat service:

~~~~ {.code-pre .command}
sudo initctl restart tomcat
~~~~

Access the Web Interface
------------------------

Now that Tomcat is up and running, let's access the web management interface in a web browser. You can do this by accessing the public IP address of the server, on port 8080:

~~~~ {.code-pre}
Open in web browser:http://server_IP_address:8080
~~~~

You will see something like the following image:

![Tomcat root](https://assets.digitalocean.com/articles/tomcat8_ubuntu/splashscreen.png)

As you can see, there are links to the admin webapps that we configured an admin user for.

Let's take a look at the Manager App, accessible via the link or `http://server_IP_address:8080/manager/html`:

![Tomcat Web Application Manager](https://assets.digitalocean.com/articles/tomcat8_ubuntu/manager.png)

The Web Application Manager is used to manage your Java applications. You can Start, Stop, Reload, Deploy, and Undeploy here. You can also run some diagnostics on your apps (i.e. find memory leaks). Lastly, information about your server is available at the very bottom of this page.

Now let's take a look at the Host Manager, accessible via the link or `http://server_IP_address:8080/host-manager/html/`:

![Tomcat Virtual Host Manager](https://assets.digitalocean.com/articles/tomcat8_ubuntu/host-manager.png)

From the Virtual Host Manager page, you can add virtual hosts to serve your applications from.

Conclusion
----------

Your installation of Tomcat is complete! Your are now free to deploy your own Java web applications!

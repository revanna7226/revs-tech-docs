Jenkins 
=============

Installation:
    Follow `Installation Procedure <https://www.jenkins.io/doc/book/installing/linux/#debianubuntu>`_ to install Jenkins on Ubuntu.

Uninstallation:

    .. code-block:: bash

        sudo apt-get remove --purge jenkins
        rm -rf /var/lib/jenkins

    Manage the Jenkins service on a Linux/Ubuntu system that uses systemd

    .. code-block:: bash

        sudo systemctl daemon-reload
        sudo systemctl restart jenkins
        sudo systemctl status jenkins


Commands:

    .. code-block:: bash

        Java Path:

        /lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.amzn2.0.1.x86_64

        Maven Path:

        /usr/share/maven

        Git Path:

        /usr/bin/git

        Assign Root User and Permissions:

        vi /etc/sysconfig/jenkins

        chown -R root:root /var/lib/jenkins
        chown -R root:root /var/cache/jenkins
        chown -R root:root /var/log/jenkins

        service jenkins restart


        Tomcat Installation and Integration:

        yum install tomcat

        yum install tomcat-webapps tomcat-admin-webapps 

        vi /usr/share/tomcat/conf/tomcat-users.xml

        Uncomment Admin roles and user and Add:

        <user username="deployer" password="deployer" roles="manager-script" />

        Deploy war/ear to a container:

        **/java-web-project.war










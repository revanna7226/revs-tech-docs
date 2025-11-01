Introduction
==========================

Docker tutorialspoint: `Link Notes <https://www.tutorialspoint.com/docker/index.htm/>`__

Docker Architecture
-----------------------

.. image:: https://media.geeksforgeeks.org/wp-content/uploads/20221205115118/Architecture-of-Docker.png
   :align: center

Docker:
    - Docker is an open-source containerization platform that allows developers to create, deploy, and manage applications inside lightweight, portable, and self-sufficient containers
    - It simplifies the process of developing, shipping, and running applications by abstracting them from the underlying infrastructure, making them run seamlessly regardless of the host environment.

Docker Daemon: 
    - The Docker Daemon is a background service that manages Docker containers, images, networks, and storage volumes. 
    - It listens for Docker API requests and performs the requested operations.

Docker Client: 
    - The Docker Client is a command-line interface (CLI) that allows users to interact with the Docker Daemon. 
    - Users can run Docker commands using the Docker Client, which sends requests to the Docker Daemon.

Docker Registries: 
      - Docker Registries are repositories where Docker images are stored and distributed.
      - The most popular public registry is Docker Hub, but private registries can also be set up.

Docker Images:
      - Docker Images are read-only templates that contain the instructions for creating a Docker container. 
      - They include the application code, runtime, libraries, and dependencies needed to run the application.

Docker Containers:
      - Docker Containers are lightweight, standalone, and executable packages that include everything needed to run a piece of software.
      - Containers are created from Docker Images and can be started, stopped, and deleted as needed.

Dockerfile:
      - A Dockerfile is a text file that contains a set of instructions for building a Docker Image.
      - It specifies the base image, application code, dependencies, and configuration needed to create the image.

Dockerizing a Simple Java Application
--------------------------------------

Build Image of a Java application Manually:
++++++++++++++++++++++++++++++++++++++++++++++++

.. code-block:: bash

   # Run a base image (e.g., OpenJDK)
   docker run -it openjdk:21-ea-oracle /bin/bash

   # Inside the container, create a directory for the application
   mkdir /app

   # Copy the Jar into the container
   docker cp target/docker-demo.jar <container_id>:/app

   # Create an image from the container
   docker commit <container_id> revannarsn/docker-demo:v1

   # Create a image from the container with a specific command
   docker commit -c 'CMD ["java","-jar","/app/docker-demo.jar"]' <container_id> revannarsn/docker-demo:v2

   # Run the newly created image
   # mention port mapping using -p <host_port>:<container_port>
   docker run -p 8080:8080 revannarsn/docker-demo:v2
   
   # Access the application in a web browser or via curl
   curl http://localhost:8080


Build Image of a Java application using Dockerfile:
+++++++++++++++++++++++++++++++++++++++++++++++++++++

.. code-block:: dockerfile

   # Use an official OpenJDK runtime as a parent image
   FROM openjdk:21-ea-oracle

   # Set the working directory in the container
   WORKDIR /app

   # Copy the application JAR file into the container at /app
   COPY target/docker-demo.jar /app/docker-demo.jar

   # Make port 8080 available to the world outside this container
   EXPOSE 8080

   # Run the application when the container launches
   CMD ["java", "-jar", "/app/docker-demo.jar"]

.. code-block:: bash

   # Build the Docker image from the Dockerfile
   docker build -t revannarsn/docker-demo:v3 .

   # Run the newly created image
   # mention port mapping using -p <host_port>:<container_port>
   docker run -p 8080:8080 revannarsn/docker-demo:v3

   # Access the application in a web browser or via curl
   curl http://localhost:8080

Dockerizing a Spring App which uses MySQL Database
++++++++++++++++++++++++++++++++++++++++++++++++++++++

Spring Boot application.properties:

.. code-block:: properties

   spring.application.name=student-app
   server.port=8091

   spring.datasource.url=jdbc:mysql://mysql:3306/studentdb
   spring.datasource.username=revsgn
   spring.datasource.password=revsgn123
   spring.jpa.hibernate.ddl-auto=create
   spring.h2.console.enabled=true
   spring.jpa.show-sql=true
   spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
   spring.sql.init.mode=always
   spring.sql.init.platform=mysql
   spring.jpa.defer-datasource-initialization=true

Dockerfile for Spring App:

.. code-block:: dockerfile

   # Use an official OpenJDK runtime as a parent image
   FROM openjdk:21-ea-oracle

   # Set the working directory in the container
   WORKDIR /app

   # Copy the application JAR file into the container at /app
   COPY target/student-app.jar /app/student-app.jar

   # Make port 8080 available to the world outside this container
   EXPOSE 8091

   # Run the application when the container launches
   CMD ["java", "-jar", "/app/student-app.jar"]

Docker Compose file to run both Spring App and MySQL Database:

.. code-block:: yaml

   version: "3.8"

   services:
      app:
         build: .
         ports:
            - "8091:8091"
         depends_on:
            - mysql

      mysql:
         image: mysql:8.0
         environment:
            MYSQL_DATABASE: studentdb
            MYSQL_USER: revsgn
            MYSQL_PASSWORD: revsgn123
         ports:
            - "3308:3306"

         volumes:
            - postgres_revs_data:/var/lib/mysql/data

   volumes:
      postgres_revs_data:

In Docker Compose version 3.8, if you do not explicitly define any networks, 
Docker Compose automatically creates a default network named [project-name]_default for the whole application. 
All services in the Compose file are connected to this default network and can communicate using their service names as hostnames.

.. code-block:: bash

   # Check the created networks
   docker network ls

   # Example output:
   NETWORK ID     NAME                  DRIVER    SCOPE
   ...
   8ff3f46e9e98   appnet                bridge    local
   8ff3f46e9e98   student-app_default   bridge    local

Build and Run using Docker Compose:

.. code-block:: bash

   # Build and start the containers in detached mode
   docker-compose up --build

   # Check the running containers
   docker ps

   # Access the Spring App in a web browser or via curl
   curl http://localhost:8091/students

   # To stop the containers
   docker-compose down
Docker Compose
========================

Docker Compose is a tool that simplifies defining and running multi-container Docker applications using a single YAML configuration file, 
typically named docker-compose.yml. It allows users to define the services, networks, and volumes that make up an application stack, 
which can then be managed with straightforward commands

Sample Definition of Compose file

.. code-block:: yaml

    name: revs-app
    services:
        web-dummy:
            image: httpd
            container_name: mywebserver
            networks:
                - webservernw
            ports:
                - "8080:80"

        cache-service:
            image: redis
            container_name: mycache
            networks:
                - cacheservernw
            ports:
                - "6379:6379"

        web:
            image: nginx
            volumes:
                - web_data:/usr/share/nginx/html # Mount named volume to container path

        db:
            image: mysql
            environment:
                MYSQL_ROOT_PASSWORD: example
            volumes:
                - db_data:/var/lib/mysql # Mount named volume to container path

    networks:
        webservernw:
            driver: bridge
        cacheservernw:
            driver: bridge
    # To use an existing Docker network (named revs-app) in your docker-compose.yml
    # instead of defining a new network, you need to declare it as an external network.
    # This tells Docker Compose to use the existing network managed outside Compose.
    # networks:
    #   revs-app:
    #     external: true

    volumes:
        web_data: # Define named volume web_data
        db_data: # Define named volume db_data

Compose Microservices with Mysql DB

.. code-block:: yaml

    name: mysql-database # name of docker-compose file
    version: "3" # version of docker-compose file

    services:
        # flight-service: # assignment - compose flight service as assignment
        product-service:
            container_name: product-app
            image: revannarsn/product-app:1.0
            restart: on-failure
            environment:
                WAIT-HOSTS: mysql:3306
            ports:
                - 10666:9090
            depends_on:
                - mysql-db-service
                - coupon-service
        coupon-service:
            container_name: coupon-app
            image: revannarsn/coupon-app:1.0
            restart: on-failure
            environment:
                WAIT-HOSTS: mysql:3306
            ports:
                - 10555:9091
            depends_on:
                - mysql-db-service
        mysql-db-service: # name of service
            container_name: docker-mysql-db # name of container
            image: mysql:latest # mysql image
            restart: always # restarts container if it crashes
            environment:
                MYSQL_DATABASE: mydb
                MYSQL_USER: myuser
                MYSQL_PASSWORD: mypassword
                MYSQL_ROOT_HOST: "%" # % means any ip can connect to db
            ports:
                - "6677:3306" #
            volumes:
                - ./sql-data:/docker-entrypoint-initdb.d # runs any .sql present inside sql-data folder
            healthcheck:
                test: ["CMD", "mysqladmin", "ping", "-h", "127.0.0.1"] # runs mysqladmin ping -h 127.0.0.1 command to check if db is running
                interval: 4s # checks every 4 seconds if db is running
                timeout: 20s # times out after 20 seconds if db is not running
                retries: 5 # retries 5 times if db is not running


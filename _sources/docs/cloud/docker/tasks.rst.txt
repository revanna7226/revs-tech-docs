Tasks
=======================

Launching MYSQL container:

   .. code-block:: bash

      docker run -d -p 6666:3306 --name=docker-mysql --env="MYSQL_ROOT_PASSWORD=test1234" --env="MYSQL_DATABASE=emp" mysql

      docker exec -it docker-mysql bash

      mysql -uroot -p 
      
      test1234

      mysql> show databases;

      use emp;

      mysql> show tables; 


Docker Volumes:

   - Docker volumes are persistent data stores managed by Docker that enable containers to save and access data beyond their individual lifespans
   - Volumes are typically stored in ``/var/lib/docker/volumes`` on Linux systems and managed independently from containers.
   - Volumes solve the stateless nature of containers by enabling persistent storage for databases, caching, file uploads, and other critical data.
   - Create new volumes using ``docker volume create`` or automatically during container creation with the ``-v`` or ``--volume`` flag.
   - List volumes with ``docker volume ls`` and inspect using ``docker volume inspect``.

Use Cases:

   - Persistent database storage (e.g., MySQL, PostgreSQL)
   - Application data for user uploads, logs, or configurations

.. code-block:: bash

   docker volume ls

   docker volume create --name=my-volume

   docker volume ls

   docker run -v my-volume:/data busybox
   // or, Bind mounts - bind 
   // docker run -dit -v /root/mydata:/tmp nginx

   docker exec -it fdb0b7c1bb0f bash

Launching MySQL Database container

.. code-block:: bash

    Setup the mysql container:
    docker run -d -p 6666:3306 --name=docker-mysql --env="MYSQL_ROOT_PASSWORD=test1234" --env="MYSQL_DATABASE=mydb" mysql
    docker exec -it docker-mysql bash
    # mysql -uroot -p
    test1234
    mysql> show databases;
    mysql> use mydb;
    mysql> show tables;
    Another Terminal:
    docker exec -i docker-mysql mysql -uroot -ptest1234 mydb <tables.sql
    Launch the Application Containers:
    docker build -f Dockerfile -t coupon_app .
    docker run -t --name=coupon-app --link docker-mysql:mysql -p 10555:9091
    coupon_app
    docker build -f Dockerfile -t product_app .
    docker run -t --name=coupon-app --link docker-mysql:mysql -p 10555:9091 coupon_app
    docker run -t --name=product-app --link docker-mysql:mysql --link coupon-app:coupon_app -p 10666:9090 product-app
    Testing:
    http://localhost:10555/couponapiapi
    http://localhost:10666/productapi
    The --link command will allow the Containers
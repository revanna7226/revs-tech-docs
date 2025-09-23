Docker Commands
=====================

#. To print Docker version information:

   .. code-block:: bash

      # Check Docker version
      docker --version

      # Detailed version information
      docker version

      # Display system-wide information
      docker info

#. To get help with Docker commands:

   .. code-block:: bash

      # General help
      docker help

      # Help for a specific command (e.g., run)
      docker run --help      

#. List Docker containers:

   .. code-block:: bash

      # To list currently running containers
      docker ps

      # To list all containers (running and stopped)
      docker ps -a

      # Alternative command to list all containers
      docker container ls

      # To list only the container IDs
      docker ps -q

#. List Docker images:

   .. code-block:: bash

      # To list all Docker images
      docker images

      # Alternative command to list images
      docker image ls

#. Search docker images on Docker Hub:

   .. code-block:: bash

      # Search for an image (e.g., openjdk)
      docker search openjdk

      # Search with a filter (e.g., official images only)
      docker search --filter=is-official=true openjdk      

#. Pull a Docker image from a registry:

   .. code-block:: bash
    
        # Pull the latest version of an image (e.g., Ubuntu)
        docker pull ubuntu
    
        # Pull a specific version of an image (e.g., Ubuntu 20.04)
        docker pull ubuntu:20.04

#. Create and start docker containers:

   .. code-block:: bash
    
        # Create a container without starting it
        docker create --name <container_name> ubuntu

        # Start an existing container
        docker start <container_name>    

#. Run a Docker container:

   .. code-block:: bash

         # Create and start a container with a specific name
         docker run --name <container_name> -d ubuntu    
      
         # Run a container in interactive mode with a terminal
         docker run -it ubuntu /bin/bash
         # Type /exit or Ctrl+D to exit the container shell
         # The container will stop when you exit the shell
      
         # Run a container in detached mode (in the background)
         docker run -d nginx
      
         # Run a container with port mapping (host:container)
         docker run -p 8080:80 nginx
      
         # Run a container with volume mapping (host:container)
         docker run -v /host/path:/container/path ubuntu

#. Stop a running Docker container:

   .. code-block:: bash
    
        # Stop a container by its container ID or name
        docker stop <container_id_or_name>

        # Stop multiple containers
        docker stop <container_id_or_name1> <container_id_or_name2>

        # Stop all running containers
        docker stop $(docker ps -q)

#. Remove a Docker container:

   .. code-block:: bash
    
        # Remove a container by its container ID or name
        docker rm <container_id_or_name>

        # Remove multiple containers
        docker rm <container_id_or_name1> <container_id_or_name2>

        # Remove all stopped containers
        docker rm $(docker ps -a -q)

#. Remove a Docker image:

   .. code-block:: bash
    
        # Remove an image by its image ID or name
        docker rmi <image_id_or_name>

        # Remove multiple images
        docker rmi <image_id_or_name1> <image_id_or_name2>

        # Remove all unused images (dangling images)
        docker image prune

#. View logs of a Docker container:

   .. code-block:: bash
    
        # View logs of a container by its container ID or name
        docker logs <container_id_or_name>

        # Follow logs in real-time
        docker logs -f <container_id_or_name>

#. Execute a command inside a running Docker container:

   .. code-block:: bash
    
        # Execute a command (e.g., bash shell) inside a container
        docker exec -it <container_id_or_name> /bin/bash

        # Execute a specific command (e.g., ls) inside a container
        docker exec <container_id_or_name> ls /path

#. Build a Docker image from a Dockerfile:

   .. code-block:: bash
    
        # Build an image with a specific tag from a Dockerfile in the current directory
        docker build -t myimage:latest .

        # Build an image from a Dockerfile in a specific directory
        docker build -t myimage:latest /path/to/dockerfile_directory

#. Tag a Docker image:

   .. code-block:: bash
    
        # Tag an image with a new name and tag
        docker tag <image_id_or_name> myrepo/myimage:latest

        # Tag an image for pushing to a specific registry
        docker tag <image_id_or_name> myregistry.com/myrepo/myimage:latest

#. Push a Docker image to a registry:

   .. code-block:: bash
    
        # Push an image to a Docker registry
        docker push myrepo/myimage:latest

        # Push an image to a specific registry
        docker push myregistry.com/myrepo/myimage:latest

#. Remove all unused Docker objects (containers, images, volumes, networks):

   .. code-block:: bash
    
        # Remove all unused Docker objects
        docker system prune

        # Remove all unused Docker objects, including volumes
        docker system prune --volumes

#. Inspect a Docker container or image:

   .. code-block:: bash
    
        # Inspect a container by its container ID or name
        docker inspect <container_id_or_name>

        # Inspect an image by its image ID or name
        docker inspect <image_id_or_name>

#. Save a Docker image to a tar file:

   .. code-block:: bash
    
        # Save an image to a tar file
        docker save -o myimage.tar myimage:latest

        # Save multiple images to a tar file
        docker save -o myimages.tar myimage1:latest myimage2:latest

#. Load a Docker image from a tar file:

   .. code-block:: bash
    
        # Load an image from a tar file
        docker load -i myimage.tar

#. Monitor Docker events in real-time:

   .. code-block:: bash
    
        # Monitor Docker events
        docker events

#. Get detailed information about Docker objects:

   .. code-block:: bash
    
        # Get detailed information about a container
        docker container inspect <container_id_or_name>

        # Get detailed information about an image
        docker image inspect <image_id_or_name>

#. Manage Docker networks:

   .. code-block:: bash
    
        # List all Docker networks
        docker network ls

        # Create a new Docker network
        docker network create mynetwork

        # Remove a Docker network
        docker network rm mynetwork

#. Manage Docker volumes:

   .. code-block:: bash
    
        # List all Docker volumes
        docker volume ls

        # Create a new Docker volume
        docker volume create myvolume

        # Remove a Docker volume
        docker volume rm myvolume

        # Remove all unused Docker volumes
        docker volume prune

#. Get help for Docker Compose commands:

   .. code-block:: bash
    
        # General help for Docker Compose
        docker-compose --help

        # Help for a specific Docker Compose command (e.g., up)
        docker-compose up --help

#. Common Docker Compose commands:

   .. code-block:: bash
    
        # Start services defined in docker-compose.yml
        docker-compose up

        # Start services in detached mode
        docker-compose up -d

        # Stop services
        docker-compose down

        # Build or rebuild services
        docker-compose build

        # View logs of services
        docker-compose logs

        # List containers managed by Docker Compose
        docker-compose ps

#. Clean up Docker system:

   .. code-block:: bash
    
        # Remove all stopped containers, unused networks, dangling images, and build cache
        docker system prune -a

        # Remove all unused images, not just dangling ones
        docker image prune -a

        # Remove all unused containers
        docker container prune

        # Remove all unused networks
        docker network prune

#. Docker Swarm commands (if using Docker Swarm):

   .. code-block:: bash
    
        # Initialize a new Swarm
        docker swarm init

        # Join a Swarm as a worker
        docker swarm join --token <worker_token> <manager_ip>:2377

        # List nodes in the Swarm
        docker node ls

        # Deploy a stack using a Docker Compose file
        docker stack deploy -c docker-compose.yml mystack

        # List services in the stack
        docker stack services mystack

        # Remove a stack
        docker stack rm mystack
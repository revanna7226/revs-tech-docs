Kubernetes
=======================

`Kubernetes is an open-source platform for automating deployment, scaling, and management of containerized applications.`

It groups containers into logical units (pods) for easy management and discovery, providing self-healing, 
flexible networking, and load balancing.

Key concepts

Pod: 
    - The smallest deployable unit, usually encapsulating one or more containers sharing storage, networking, and specification.

Service: 
    - Set of Pods with stable endpoints used for Service Discovery and Load balancing
    - ensures communication inside the cluster using internal DNS.

Ingress:
    - Set of rules for routing external HTTPS traffic to services within the cluster;
    - enables SSL termination, path-based routing, and virtual hosting.

Cluster:
    - A set of machines running Kubernetes managed workloads, providing resources, networking, and scheduling for applications.

Deployment:
    - Defines the desired state for application replicas, facilitates rolling updates and rollbacks.

Networking:
    - Kubernetes ensures that all pods can communicate with each other across nodes using cluster networking.
    - Services provide a stable network identity, typically reached by DNS names.
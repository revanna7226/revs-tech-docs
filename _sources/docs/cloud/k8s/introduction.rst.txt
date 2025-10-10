Kubernetes
=======================

- `Kubernetes is an open-source platform for automating deployment, scaling, and management of containerized applications.`
- It groups containers into logical units (pods) for easy management and discovery, providing self-healing, 
  flexible networking, and load balancing.
- K8s is maintained and licensed by `Cloud Native Computing Foundation (CNCF)`.

Kubernetes on Public Clouds:
    - Google Cloud : Google Kubernetes Engine
    - Amazon AWS: Elastic Container Service
    - Microsoft Azure: Azure Kubernetes Service

Kubernetes Cluster Architecture:
----------------------------------------

Click `here <https://kubernetes.io/docs/concepts/architecture/>`_ to know more on Kubernetes Architecture

.. image:: https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg
   :align: center

Key concepts
--------------------
Cluster:
    - A set of machines running Kubernetes managed workloads, providing resources, networking, and scheduling for applications.

Namespace:
    - Namespaces in Kubernetes provide a way to logically partition and isolate groups of resources within a single cluster. 
    - They help organize cluster resources into virtual sub-clusters, which is especially useful when multiple teams or projects share the same Kubernetes cluster.

Deployment:
    - A Deployment is a higher-level Kubernetes abstraction that manages ReplicaSets and the pods they control.
    - Defines the desired state for application replicas, facilitates rolling updates and rollbacks.
 
ReplicaSet:
    - A ReplicaSet ensures that a specified number of identical pod replicas are always running in a Kubernetes cluster.
    - If a pod fails or is deleted, the ReplicaSet automatically creates a new one to maintain the desired count. 

Pod: 
    - The smallest deployable unit, usually encapsulating one or more containers sharing storage, networking, and specification.

Service: 
    - Set of Pods with stable endpoints used for Service Discovery and Load balancing
    - ensures communication inside the cluster using internal DNS.

Ingress:
    - Set of rules for routing external HTTPS traffic to services within the cluster;
    - enables SSL termination, path-based routing, and virtual hosting.

Networking:
    - Kubernetes ensures that all pods can communicate with each other across nodes using cluster networking.
    - Services provide a stable network identity, typically reached by DNS names.
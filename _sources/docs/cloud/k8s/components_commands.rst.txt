K8s Componets and Commands
==========================

Basic K8s commands:

    .. code-block:: bash

        # Gives the details of the deployment.
        kubectl explain deployment

Pods:
    Creating a first pod using kubectl Command.

    .. code-block:: bash

        # get all resources in the cluster. Deployments, Replicaset, Pods, Services, Ingress, etc.

        kubectl get pods

        # create a new pod with name firstpod from image nginx
        kubectl run firstpod --image=nginx

        # list all available pods
        kubectl get pods

        # describe the first pod which shows all the basic info about this pod
        kubectl describe pod firstpod

        # to go inside pod
        kubectl exec -it firstpod -- /bin/bash 

        # exiting from the pod
        exit

        #  fetches the details of the pod named firstpod and outputs the entire resource definition in YAML format.
        kubectl get pod/firstpod -o yaml

    Creating Pod using Yaml File.

    .. code-block:: yaml

        # FileName: firstpod.yml
        # ----------------------
        kind: Pod
        apiVersion: v1
        # data about the resource i.e Pod
        metadata:
            name: firstpod
        # Body/details/Specificatin of the resource
        spec:
            containers:
                - name: web
                  image: httpd
                - name: db
                  image: redis 

    Create resource/Pod using yml file.

    .. code-block:: bash

        kubectl create -f firstpod.yml

        # go into the container
        kubectl exec -it firstpod web -- /bin/bash
        kubectl exec -it firstpod db -- /bin/bash




Linux Commands:
    Commands to install tools in Linux Environment.

    .. code-block:: bash

        # Command to install any tools in Linux
        apt-get update # update apt-get first and then install any tool you want
        apt-get install nano # installing nano editor
        apt-get install curl # installing curl

        curl localhost # hitting http://localhost:80 

Label and Selectors:
    - Labels in Kubernetes are key-value pairs attached to objects like pods, deployments, and services to organize and categorize resources. For example, a pod can have labels like app=frontend or env=production defined usually in the metadata section of the resource YAML.
    - Selectors are queries that allow you to filter and select Kubernetes objects based on their labels. They are widely used by deployments, services, and other controllers to target specific sets of pods or resources.

    Key points about labels and selectors:
      - Labels are used to attach identifying attributes to objects, e.g., app=frontend.
      - Selectors query these labels and come in two forms:
          - Equality-based selectors: Match exact key-value pairs with operators like =, ==, !=. Example: app=frontend
          - Set-based selectors: More flexible, match keys with value sets or existence using in, notin, exists. Example: env in (dev, test) selects pods with env label as either dev or test.
      - Labels enable grouping and management of objects without relying on their names.
      - Typical usage includes filtering pods a service should route to or defining which pods a deployment should manage.
      - Labels and selectors provide a powerful and flexible way to organize, select, and manage Kubernetes resources efficiently.
    
    Example of labels in a pod YAML:

    .. code-block:: yaml

        apiVersion: v1
        kind: Pod
        metadata:
            name: my-pod
            labels:
                app: firstapp
                release: dev
                env: local
            annotations: #  mainly for storing metadata consumed by tools like grafana, promotheusa and scripts, or info to developers/humans.
                description: "This is my frontend pod"
                logsDir: /var/log/
        spec:
            containers:
            - name: nginx-web
              image: nginx

    Example kubectl usage with selectors:
    
    .. code-block:: bash

        # get all resources with labels
        kubectl get all --show-labels

        # use selectors to get the resources with labels
        kubectl get all --selector='env=local'

        #List pods with labels
        kubectl get pods -l app=frontend
        kubectl get pods -l app=firstapp,env=local
        kubectl get all -l app=firstapp,env=local

        #List pods with label env in either dev or test:
        kubectl get pods -l 'env in (dev, test, local)'


Annotations:
    - Annotations are placed under the metadata.annotations section. As shown in the above yml file.
    - Unlike labels, annotations are primarily used for storing descriptive information, tooling data, or configuration details that don't influence Kubernetes object selection or grouping.

Namespaces: 

    .. code-block:: bash

        kubectl create namespace mynamespace
        # or
        kubectl create ns mynamespace

        # 
        kubectl config set-context --current --namespace=<namespace-name>
        kubectl get pods --namespace mynamespace

    Create Namespace using yaml file.

    .. code-block:: yaml

        apiVersion: v1
        kind: Namespace
        metadata:
            name: mynamespace

    Specify namespace where you want to create resources.

    .. code-block:: yaml

        apiVersion: v1
        kind: Pod
        metadata:
            name: mypod
            namespace: mynamespace # create this pod in mynamespace

Dry Run:
    - It simulates the creation of the Kubernetes resource without actually submitting it to the cluster.
    - By default, with --dry-run (or more explicitly --dry-run=client), it only validates the resource definition locally on the client side.
    - You get feedback about whether the resource YAML is correct and what would be created, without making any changes to the cluster.

    .. code-block:: bash

        kubectl create -f firstpod.yml --dry-run=client

Deployment:
    - Deployment

    .. code-block:: yaml

        apiVersion: apps/v1
        kind: Deployment
        metadata:
            name: mywebserver
            labels:
                app: httpd
        spec:
            replicas: 2
            selector:
                matchLabels:
                    app: httpd
            template:
                metadata:
                    labels:
                        app: httpd
                spec:
                    containers:
                      - name: myhttpd
                        image: httpd
                        ports:
                          - containerPort: 80

Service:
    - NodePort (30123): The port exposed on each Kubernetes node’s IP address to accept external traffic.
    - Service Port (80): The port on the Kubernetes Service inside the cluster. This acts as a stable virtual IP and port for routing.
    - TargetPort (80): The port on the container inside the Pod where your application is listening.
  
    **External request to port 30007 on the Node IP → Service port 80 → Container port 80**

    .. code-block:: yaml

        apiVersion: v1
        kind: Service
        metadata:
            name: webserver-service
        spec:
            type: NodePort
            selector:
                app: httpd
            ports:
                - nodePort: 30123 # external port
                  port: 80 # servuce port
                  targetPort: 80 # container port

    .. code-block:: bash

        kubectl port-forward svc/<service-name> 8080:<service-port>

        kubectl port-forward svc/myhttpd-service 8080:80

    
    - This sets up a tunnel.
    - Now, opening http://localhost:8080 in your browser will route traffic from your local port 8080 
      to the service's port 80 inside the cluster.

Rolling Updates:
    - Rolling updates in Kubernetes (K8s) are a deployment strategy designed to update applications with zero downtime. 
    - This strategy incrementally replaces the current Pods running an application with new Pods that contain the updated version. 
    - Kubernetes manages the update by creating new Pods, waiting for them to become healthy and ready, then terminating old Pods in a controlled manner.
    - The rolling update is the default and most common deployment strategy in Kubernetes, ideal for services needing high availability during frequent updates. 
    - It incrementally replaces Pods, ensuring that at least some Pods are always available to serve user traffic.

   The rolling update strategy is configured in the Deployment YAML file under the spec.strategy field. 
   This includes the type RollingUpdate and parameters maxSurge and maxUnavailable to control the update pace:

   - maxSurge: The maximum number (or percentage) of pods allowed above the desired replicas during the update.
   - maxUnavailable: The maximum number (or percentage) of pods that can be unavailable during the update.

   .. code-block:: yaml

      apiVersion: apps/v1
      kind: Deployment
      metadata:
         name: mywebserver
      spec:
         replicas: 10
         strategy:
            type: RollingUpdate
            rollingUpdate:
               maxSurge: 3
               maxUnavailable: 4
         selector:
            matchLabels:
               app: httpd
         template:
            metadata:
               labels:
               app: httpd
            spec:
               containers:
                 - name: myhttpd
                   image: httpd
                   ports:
                 - containerPort: 80

Rollbacks:
   -  Rollbacks

   .. code-block:: bash

      kubectl rollout history deployment
      deployment.apps/mywebserver
      # REVISION  CHANGE-CAUSE
      # 2         <none>
      # 3         <none>

      # To see the changes in the revison
      kubectl rollout history deployment mywebserver --revision=2
      #>> shows the revison changes

      # rollout to previous revision
      kubectl rollout undo deployment mywebserver

      # rollout to specified revision
      kubectl rollout undo deployment mywebserver --to-revision=1

Scaling:
   - Manula and auto Scaling

   .. code-block:: bash

      # manual scaling
      kubectl scale deployment mywebserver --replicas=20

Volumes:
   - emptyDir
   - nfs
   - hostPath

   .. code-block:: yaml

      apiVersion: apps/v1
      kind: Deployment
      metadata:
         name: mywebserver
      spec:
         replicas: 10
         strategy:
            type: RollingUpdate
            rollingUpdate:
               maxSurge: 3
               maxUnavailable: 4
         selector:
            matchLabels:
               app: httpd
         template:
            metadata:
               labels:
                  app: httpd
            spec:
               containers:
                  - name: myhttpd
                    image: httpd
                    ports:
                        - containerPort: 80
                    volumeMounts:
                        - name: demovol
                          mountPath: /data
               volumes:
                  - name: demovol
                    hostPath:
                        path: /data
                        type: Directory

   .. code-block:: bash

      docker run -it --rm --privileged --pid=host justincormack/nsenter1


ConfigMap:
   - application configurations

   .. code-block:: yaml

      # demo-configmap.yml
      apiVersion: v1
      kind: ConfigMap
      metadata:
         name: demo-configmap
      data:
         initdb.sql:
            select * from product;
            create table coupon();
         keys:
            sbdhgbfhbskdjfnjsndkjbsdj
            bdfhbsdfbsbdfjksndnsdjnsldgn

   .. code-block:: yaml

      # webserver.yml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
         name: mywebserver
      spec:
         replicas: 10
         strategy:
            type: RollingUpdate
            rollingUpdate:
               maxSurge: 3
               maxUnavailable: 4
         selector:
            matchLabels:
               app: httpd
         template:
            metadata:
               labels:
                  app: httpd
            spec:
               containers:
                  - name: myhttpd
                    image: httpd
                    ports:
                        - containerPort: 80
                    volumeMounts:
                        - name: demovol
                          mountPath: /data
                        - name: demo-configmap-vol
                          mountPath: /data/config
               volumes:
                  - name: demovol
                    hostPath:
                        path: /data
                        type: Directory
                  - name: demo-configmap-vol
                    configMap:
                        name: demo-configmap



Secret:
   - to store secrets in K8s cluster

   .. code-block:: yaml

      apiVersion: v1
      kind: Secret
      metadata:
         name: demo-secret
      type: Opaque # orbitory key-value pair
      data:
         userName: 
            cmV2c2duCg== #  echo "revsgn" | base64
         password: 
            cmV2c2duCg== #  echo "revsgn" | base64

   How to use secrete?
      
   .. code-block:: yaml

         .
         .
         .
         template:
            metadata:
               labels:
                  app: httpd
            spec:
               containers:
                  - name: myhttpd
                    image: httpd
                    ports:
                        - containerPort: 80
                    volumeMounts:
                        - name: demovol
                          mountPath: /data
                        - name: demo-configmap-vol
                          mountPath: /data/config
                        - name: demo-secret-vol
                          mountPath: /data/secret
               volumes:
                  - name: demovol
                    hostPath:
                        path: /data
                        type: Directory
                  - name: demo-configmap-vol
                    configMap:
                        name: demo-configmap
                  - name: demo-secret-vol
                    secret:
                        secretName: demo-secret

   



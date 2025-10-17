Introduction
================

Helm Commands:

.. code-block:: bash

    helm repo list

    helm repo add bitnami https://charts.bitnami.com/bitnami

    helm repo list

    helm repo remove bitnami

    helm repo add bitnami https://charts.bitnami.com/bitnami


    Search the repository:

    helm search repo mysql

    helm search repo database

    helm search repo database --versions


    Install a package:

    kubectl get pods

    (Below Two commands - In a Different Shell/Commandline window/tab)

    minikube ssh

    docker images

    helm install mydb bitnami/mysql

    Check the cluster:

    kubectl get pods

    minikube ssh

    docker images

    To check the installation status:

    helm status mydb



    --------------------------------------------

    To Upgrade:

    ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)

    helm upgrade --namespace default mysql-release bitnami/mysql --set auth.rootPassword=$ROOT_PASSWORD

    -------

    helm uninstall mysql-release


Updates:

.. code-block:: bash

    #⚠ WARNING: Since August 28th, 2025, only a limited subset of images/charts are available for free.
    #Subscribe to Bitnami Secure Images to receive continued support and security updates
    #More info at https://bitnami.com and https://github.com/bitnami/containers/issues/83267

    #Solution:
    Append --set image.repository=bitnamilegacy/<ChartName> to your helm install command

    #Example:
    helm install mydb bitnami/mysql --set image.repository=bitnamilegacy/mysql

output:

.. code-block:: text

    NAME: mydb
    LAST DEPLOYED: Wed Oct 15 06:22:10 2025
    NAMESPACE: default
    STATUS: deployed
    REVISION: 1
    TEST SUITE: None
    NOTES:
    CHART NAME: mysql
    CHART VERSION: 14.0.3
    APP VERSION: 9.4.0

    ⚠ WARNING: Since August 28th, 2025, only a limited subset of images/charts are available for free.
        Subscribe to Bitnami Secure Images to receive continued support and security updates.
        More info at https://bitnami.com and https://github.com/bitnami/containers/issues/83267

    ** Please be patient while the chart is being deployed **

    Tip:

    Watch the deployment status using the command: kubectl get pods -w --namespace default

    Services:

    echo Primary: mydb-mysql.default.svc.cluster.local:3306

    Execute the following to get the administrator credentials:

    echo Username: root MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 -d)

    To connect to your database:

    1. Run a pod that you can use as a client:

        kubectl run mydb-mysql-client --rm --tty -i --restart='Never' --image  docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1 --namespace default --env MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD --command -- bash

    2. To connect to primary service (read/write):

        mysql -h mydb-mysql.default.svc.cluster.local -uroot -p"$MYSQL_ROOT_PASSWORD"






    WARNING: There are "resources" sections in the chart not set. Using "resourcesPreset" is not recommended for production. For production installations, please set the following values according to your workload needs:
    - primary.resources
    - secondary.resources
    +info https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

    ⚠ SECURITY WARNING: Original containers have been substituted. This Helm chart was designed, tested, and validated on multiple platforms using a specific set of Bitnami and Tanzu Application Catalog containers. Substituting other containers is likely to cause degraded security and performance, broken chart features, and missing environment variables.

    Substituted images detected:
    - docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1

    ⚠ WARNING: Original containers have been substituted for unrecognized ones. Deploying this chart with non-standard containers is likely to cause degraded security and performance, broken chart features, and missing environment variables.

    Unrecognized images:
    - docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1    
   
Helm Commands
================

#. Providing Custom Values to Charts

    .. code-block:: bash

        # You can provide custom values to a chart using the `--set` flag during installation.
        #Example:
        helm install mydb bitnami/mysql --set image.repository=bitnamilegacy/mysql auth.rootPassword=test1234

        #Recommended: Use a values.yaml file to manage complex configurations.
        #Example:
        helm install mydb -f values.yml bitnami/mysql

    values.yml

    .. code-block:: yaml

        auth:
            rootPassword: test1234
        image:
            repository: bitnamilegacy/mysql

#. Helm Upgrade

    .. code-block:: bash

        # Update helm repositories to get the latest charts.
        helm repo update

        helm list

        helm status mydb

        # To upgrade a release with new custom values, use the `helm upgrade` command.
        #Example:
        helm upgrade mydb -f values.yaml bitnami/mysql

        # You can also upgrade using the `--set` flag.
        #Example:
        helm upgrade mydb bitnami/mysql --set image.repository=bitnamilegacy/mysql auth.rootPassword=newpassword1234

#. Release records:

    .. code-block:: bash

        helm list
        #NAME    NAMESPACE       REVISION        UPDATED                                 STATUS     CHART            APP VERSION
        #mydb    default         2               2025-10-15 07:34:53.237405933 +0000 UTC deployed   mysql-14.0.3     9.4.0
        kubectl get secrets
        #NAME                         TYPE                 DATA   AGE
        #mydb-mysql                   Opaque               2      19m
        #sh.helm.release.v1.mydb.v1   helm.sh/release.v1   1      19m
        #sh.helm.release.v1.mydb.v2   helm.sh/release.v1   1      6m8s

#. Helm Uninstall

    .. code-block:: bash
        
        # To uninstall a release, use the `helm uninstall` command.
        #Example:
        helm uninstall mydb --keep-history

        helm list        

#. Release History and Rollback

    .. code-block:: bash

        # To view the history of a release, use the `helm history` command.
        #Example:
        helm history mydb

        # To rollback to a previous version of a release, use the `helm rollback` command.
        #Example:
        helm rollback mydb 1        
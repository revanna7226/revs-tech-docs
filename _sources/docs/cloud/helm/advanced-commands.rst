Advanced Commands 
========================

#. Helm Release Workflow

    helm install mydb bitnami/mysql

    - Load the charts and it's dependencies
    - Create a new release named `mydb`
    - Deploy the resources defined in the chart to the Kubernetes cluster
    - Track the release in the cluster
    - Generate a unique revision number for the release
    - Store the release information in a Kubernetes secret
    - Output the status of the release, including the resources created and any notes or instructions for accessing the application.
  
    .. code-block:: bash

        # Example commands to illustrate Helm release workflow

        # Install a chart and create a new release
        helm install myapp bitnami/nginx

        # View the status of the release
        helm status myapp

        # Upgrade the release with new values
        helm upgrade myapp bitnami/nginx --set service.type=LoadBalancer

        # View the history of the release
        helm history myapp

        # Roll back to a previous version of the release
        helm rollback myapp 1

        # Uninstall the release and keep its history
        helm uninstall myapp --keep-history

#. Helm Revisions

    .. code-block:: bash

        helm install -f values.yml mydb bitnami/mysql
        helm upgrade -f values.yml mydb bitnami/mysql
        kubectl get secrets # returns release secrets now
        kubectl get secret sh.helm.release.v1.mydb.v1 -o yaml
        kubectl get secrets
        kubectl get secret sh.helm.release.v1.mydb.v2 -o yaml
        helm upgrade -f values.yml mydb bitnami/mysql
        kubectl get secrets
        kubectl get secret sh.helm.release.v1.mydb.v3 -o yaml

#. Helm Dry Run

    .. code-block:: bash

        # The `--dry-run` flag simulates an install, upgrade, or uninstall operation without making any changes to the cluster.
        # Example:
        helm install mydb bitnami/mysql --dry-run

        helm upgrade mydb bitnami/mysql --set auth.rootPassword=newpassword1234 --dry-run

        helm uninstall mydb --dry-run --keep-history        

    .. code-block:: text

        # Output:
        # This will display the resources that would be created, modified, or deleted, along with any notes or instructions for accessing the application, without actually performing the operation.

#. Helm Template

    .. code-block:: bash

        # The `helm template` command renders the templates in a chart and outputs the resulting Kubernetes manifests to standard output.
        # Example:
        helm template mydb bitnami/mysql

        helm template mydb bitnami/mysql --set auth.rootPassword=newpassword1234

    .. code-block:: text

        # Output:
        # This will display the rendered Kubernetes manifests, which can be reviewed or modified before applying them to the cluster using `kubectl apply -f -`.

#. Helm get

    This will display the requested information about the release, which can be useful for troubleshooting or auditing purposes.

    .. code-block:: bash

        # The `helm get` command retrieves information about a release, including its values, manifest, hooks, and notes.
        # Example:
        # get all information about the release
        helm get all mydb

        # get user-supplied values for the release
        helm get values mydb

        # get system-generated values for the release
        helm get values mydb --all

        # get user-supplied values for the release
        helm get values mydb --revision 1

        # get the rendered templates for the release
        helm get manifest mydb

        # get the hooks for the release
        helm get hooks mydb

        # get the notes for the release
        helm get notes mydb

#. Helm history

    This will display a table with the revision history of the release, which can be useful 
    for tracking changes and rolling back to previous versions if needed.

    .. code-block:: bash

        # The `helm history` command shows the revision history of a release, 
        # including the revision number, date, status, chart version, and app version.
        # Example:
        helm history mydb

        # Limit the number of revisions displayed
        helm history mydb --max 5

#. Helm rollback

    Helm Rollback is a command used to revert a Helm release to a previous version. 
    This is useful when a deployment or upgrade has caused issues, and you want to restore the application to a known good state.

    .. code-block:: bash

        # The `helm rollback` command rolls back a release to a previous revision.
        # Example:
        helm rollback mydb 1

        # Rollback to the previous revision
        helm rollback mydb

        # Rollback and wait until all resources are in a ready state before marking the release as successful
        helm rollback mydb 1 --wait


#. Install Helm Chart in a specific NAMESPACE

    .. code-block:: bash

        # To create a namespace in Kubernetes, use the `kubectl create namespace` command.
        # Example:
        kubectl create namespace my-namespace

        # Verify the namespace was created
        kubectl get namespaces

        #-------------------------------------------------------------------------------#

        # To install a Helm chart in a specific namespace, use the `--namespace` flag with the `helm install` command. 
        # The --create-namespace flag can be used to create the namespace if it doesn't already exist.
        # Example:
        helm install mydb bitnami/mysql --namespace my-namespace --create-namespace

        # Verify the release was installed in the correct namespace
        helm list --namespace my-namespace

#. Helm Install or Upgrade

    .. code-block:: bash

        # The `helm upgrade --install` command upgrades a release if it exists, or installs it if it doesn't.
        # Example:
        helm upgrade --install mydb bitnami/mysql --set auth.rootPassword=newpassword1234

        # Verify the release was installed or upgraded
        helm list   

#. Helm Uninstall with Keep History

    .. code-block:: bash

        # The `helm uninstall --keep-history` command uninstalls a release but retains its history in case you want to reinstall it later.
        # Example:
        helm uninstall mydb --keep-history

        # Verify the release was uninstalled
        helm list --all

#. Helm generate release name

    .. code-block:: bash

        # The `helm install` command can automatically generate a release name if you don't provide one.
        # Example:
        helm install bitnami/mysql

        helm install --generate-name bitnami/mysql --name-template 'my-app-{{randAlphaNum 5 | lower}}'

        # Verify the release was installed with a generated name
        helm list

#. Helm Wait and Timeout

    If you want helm to wait for the pods to be up and running when you do a helm install which of the following options should be used

    .. code-block:: bash

        # The `--wait` flag makes Helm wait until all resources are in a ready state before marking the release as successful.
        # The `--timeout` flag specifies the maximum time to wait for the release to be ready.
        # Example:
        helm install mydb bitnami/mysql --wait --timeout 300s

        # Verify the release was installed and is in a ready state
        helm list

#. Helm Atomic Install

    used to rollback to a previous successful installation if the current installation fails

    .. code-block:: bash

        # The `--atomic` flag makes Helm automatically roll back the release if the installation or upgrade fails.
        # Example:
        helm install mydb bitnami/mysql --atomic --wait --timeout 300s

        # Verify the release was installed and is in a ready state
        helm list

#. Helm Force Upgrade

    don't use it for CICD pipelines as it may lead to downtime.

    .. code-block:: bash

        # The `--force` flag forces resource updates through a replacement strategy. 
        # This can be useful when you want to ensure that all resources are updated, even if they have not changed.
        # Example:
        helm upgrade mydb bitnami/mysql --set auth.rootPassword=newpassword1234 --force

        # Verify the release was upgraded
        helm list        
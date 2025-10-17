Create Charts
==================

#. Create a New Chart

    .. code-block:: bash

        # To create a new Helm chart, use the `helm create` command followed by the name of the chart you want to create.
        helm create mychart
        # This will create a directory named `mychart` with a basic Helm chart structure.
        ls mychart
        # You can explore the contents of the chart directory using the `tree` command (if available) or by listing the files.
        tree mychart
        # Example output:
        revschart
        ├── Chart.yaml # Information about your chart
        ├── charts # Directory to hold any dependent charts
        ├── templates # Directory for Kubernetes manifest templates
        │   ├── NOTES.txt # Instructions displayed after installation
        │   ├── _helpers.tpl # Template helpers for the chart
        │   ├── deployment.yaml # Deployment resource template
        │   ├── hpa.yaml # Horizontal Pod Autoscaler template
        │   ├── httproute.yaml # HTTPRoute resource template
        │   ├── ingress.yaml # Ingress resource template
        │   ├── service.yaml # Service resource template
        │   ├── serviceaccount.yaml # ServiceAccount resource template
        │   └── tests # Directory for test files
        │       └── test-connection.yaml # Test resource template
        └── values.yaml # Default configuration values for the chart

        4 directories, 11 files

#. Install a Chart

    .. code-block:: bash

        # To install the chart you created, use the `helm install` command followed by a release name and the path to the chart directory.
        helm install myrelease mychart
        # This will deploy the resources defined in your chart to your Kubernetes cluster.
        # You can check the status of your release using:
        helm status myrelease
        # To see the resources created by your chart, you can use:
        kubectl get all
        # Example output:
        NAME                                   READY   STATUS    RESTARTS   AGE
        pod/myrelease-mychart-5d5b6f7c7b-abcde   1/1     Running   0          2m

        NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
        service/myrelease-mychart ClusterIP   10.96.123.456   <none>        80/TCP     2m

        NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/myrelease-mychart   1/1     1            1           2m

        NAME                                         DESIRED   CURRENT   READY   AGE
        replicaset.apps/myrelease-mychart-5d5b6f7c7b   1         1         1       2m

#. Chart yaml

    A Sample Chart.yml

    .. code-block:: yaml

        apiVersion: v2 # The chart API version
        name: revschart # The name of the chart
        description: A Helm chart for Kubernetes # A brief description of the chart
        icon: https://www.freeiconspng.com/uploads/website-icon-8.png # URL to an icon file for the chart
        keywords: # Keywords to help search for the chart
            - revsapp
            - myapp
            - sample-app
            - demoapp
        home: http://revsgn.com/ # The URL of the project homepage
        sources: # A list of URLs to the source code for the project
            - http://revsgn.com/sources
        maintainers: # A list of maintainers for the chart
            - name: revsgn
              email: revsn@email.com
            - name: pushpab
              email: pushpab@email.com
        # A chart can be either an 'application' or a 'library' chart.
        #
        # Application charts are a collection of templates that can be packaged into versioned arch>
        # to be deployed.
        #
        # Library charts provide useful utilities or functions for the chart developer. They're inc>
        # a dependency of application charts to inject those utilities and functions into the rende>
        # pipeline. Library charts do not define any templates and therefore cannot be deployed.
        type: application

        # This is the chart version. This version number should be incremented each time you make c>
        # to the chart and its templates, including the app version.
        # Versions are expected to follow Semantic Versioning (https://semver.org/)
        version: 0.1.0

        # This is the version number of the application being deployed. This version number should >
        # incremented each time you make changes to the application. Versions are not expected to
        # follow Semantic Versioning. They should reflect the version the application is using.
        # It is recommended to use it with quotes.
        appVersion: "1.16.0"

#. Helm package

    .. code-block:: yaml

        # To package a Helm chart, use the `helm package` command followed by the path to the chart directory.
        helm package mychart

        # If your chart has dependencies, you can update them before packaging using the `--dependency-update` flag.
        helm package mychart --dependency-update

        # You can also specify a revision number for the packaged chart using the `--revision` flag.
        helm package mychart --revision 2

        # You can specify a version for the packaged chart using the `--version` flag.
        helm package mychart --version 1.0.0

        # To specify an output directory for the packaged chart, use the `-d` or `--destination` flag.
        helm package mychart -d ./output-directory

        # This will create a `.tgz` file in the current directory, which is a packaged version of your chart.
        ls
        # Example output:
        mychart-0.1.0.tgz

#. Helm ignore

    # The `.helmignore` file is used to specify files and directories that should be ignored when packaging a Helm chart.
    # It follows the same pattern as a `.gitignore` file.

    # Example `.helmignore` file:

    .. code-block:: text

        # Ignore all files with a .log extension
        *.log

        # Ignore the `tmp` directory
        tmp/

        # Ignore all files in the `tests` directory
        tests/*

        # Ignore specific files
        secret-values.yaml
        README.md

        # Ignore all hidden files (files starting with a dot)
        .*

        # Ignore all files in the `docs` directory except for `docs/important.md`
        docs/*
        !docs/important.md

        # Ignore all `.txt` files in the `config` directory
        config/*.txt        

#. Helm Lint

    .. code-block:: bash

        # To validate a Helm chart, use the `helm lint` command followed by the path to the chart directory.
        helm lint mychart

        # This command checks the chart for common issues and best practices, and it will output any warnings or errors it finds.
        # Example output:
        == Linting mychart ==
        [INFO] Chart.yaml: icon is recommended
        [INFO] Chart.yaml: keywords are recommended
        
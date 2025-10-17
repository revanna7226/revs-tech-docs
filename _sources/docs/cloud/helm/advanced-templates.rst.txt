Advanced Chart Topics
============================

#. Adding Dependencies to Charts:

  Helm charts can depend on other charts. This is useful for including 
  common components, such as databases or monitoring tools, in your 
  application.

  To add a dependency, you need to define it in the `Chart.yaml` file 
  of your chart. Here is an example of how to add a dependency:

  .. code-block:: yaml

     dependencies:
       - name: redis
         version: "14.8.8"
         repository: "https://charts.bitnami.com/bitnami"

  After defining the dependency, you need to run the following 
  command to update the dependencies:

  .. code-block:: bash

     helm dependency update mychart/

  This will download the specified version of the Redis chart and 
  place it in the `charts/` directory of your chart.

#. Using version constraints:

  When specifying dependencies, you can use version constraints to 
  ensure that your chart uses a compatible version of the dependency. 
  Helm supports semantic versioning, and you can use operators like 
  `>=`, `<=`, `~`, and `^` to define version ranges.

  For example, to specify that your chart requires at least version 
  14.0.0 of the Redis chart, you can use the following constraint:

  .. code-block:: yaml

     dependencies:
       - name: redis
         version: ">=14.0.0"
         repository: "https://charts.bitnami.com/bitnami"

#. Using condition and tags:

  You can use the `condition` and `tags` fields in the dependency 
  definition to control when a dependency is included in the chart. 
  The `condition` field allows you to specify a value in the `values.yaml` 
  file that determines whether the dependency is enabled or disabled. 
  The `tags` field allows you to group dependencies and enable or disable 
  them based on tags.

  Here is an example of using the `condition` field:

  .. code-block:: yaml

     dependencies:
       - name: redis
         version: "14.8.8"
         repository: "https://charts.bitnami.com/bitnami"
         condition: redis.enabled

  In this example, the Redis dependency will only be included if the 
  `redis.enabled` value in the `values.yaml` file is set to `true`.

#. Using repo name:

    When adding a dependency, you can use a repository name instead of 
    the full URL. This is useful if you have added the repository to 
    your Helm client using the `helm repo add` command.
    
    For example, if you have added the Bitnami repository with the name 
    `bitnami`, you can specify the dependency as follows:
    
    .. code-block:: yaml
    
         dependencies:
         - name: redis
             version: "14.8.8"
             repository: "bitnami"

#. Using multiple conditional dependencies:

    You can add multiple dependencies to your chart by defining them 
    in the `dependencies` section of the `Chart.yaml` file. Each 
    dependency should be defined as a separate item in the list.
    
    Here is an example of a chart with multiple dependencies:
    In this example, the Redis dependency will only be included if the 
    `tags.enabled` value in the `values.yaml` file is set to `true`.
    
    .. code-block:: yaml
    
         dependencies:
         - name: redis
             version: "14.8.8"
             repository: "https://charts.bitnami.com/bitnami"
             tags:
               - enabled
         - name: postgresql
             version: "10.3.11"
             repository: "https://charts.bitnami.com/bitnami"
             tages:
               - enabled

#. Pass values to dependencies:

    You can pass custom values to the dependencies of your chart by 
    defining them in the `values.yaml` file of your chart. You can 
    specify values for each dependency using the name of the dependency 
    as a prefix.
    
    Here is an example of how to pass custom values to the Redis 
    dependency:
    
    .. code-block:: yaml
    
         redis:
           auth:
             password: mypassword
           master:
             persistence:
               enabled: true
               size: 10Gi
         mysql:
           auth:
             rootPassword: mypassword
           primary:
             service:
               type: NodePort
               nodePort: 30075

#. Read Values from Dependencies

    You can read values from the dependencies of your chart using 
    the `{{ .Values.<dependency-name>.<value-path> }}` syntax in your 
    templates. This allows you to access values defined in the 
    `values.yaml` file of the dependency chart.
    
    Here is an example of how to read a value from the Redis dependency:
    
    .. code-block:: yaml
    
         apiVersion: v1
         kind: ConfigMap
         metadata:
           name: myconfigmap
         data:
           redis-password: {{ .Values.redis.auth.password | quote }}
          
#. Exporting Values to Dependencies

    You can export values from your chart to its dependencies using 
    the `export` field in the `values.yaml` file of your chart. This 
    allows you to share values between your chart and its dependencies.
    
    Here is an example of how to export a value to the Redis dependency:
    
    .. code-block:: yaml
    
         export:
           myvalue: "somevalue"
    
    In the Redis dependency, you can access the exported value using 
    the `{{ .Values.myvalue }}` syntax in your templates.

#. Exporting Values to Parent Chart

    You can export values from a dependency chart to its parent chart 
    using the `export` field in the `values.yaml` file of the 
    dependency chart. This allows you to share values between the 
    dependency chart and its parent chart.
    
    Here is an example of how to export a value from the Redis 
    dependency chart:
    
    .. code-block:: yaml
    
         export:
           redisHost: "myredishost"
    
    In the parent chart, you can access the exported value using 
    the `{{ .Values.redisHost }}` syntax in your templates.

#. Using Global Values

    You can define global values in the `values.yaml` file of your 
    chart using the `global` key. Global values are accessible from 
    any template in your chart and its dependencies.
    
    Here is an example of how to define and use a global value:
    
    .. code-block:: yaml
    
         global:
           appName: "myapp"
    
    In your templates, you can access the global value using the 
    `{{ .Values.global.appName }}` syntax.

#. Using Values which are not exported

    If a dependency chart does not export a value, you can still 
    access its values using the `{{ .Values.<dependency-name>.<value-path> }}` 
    syntax in your templates. However, this approach is less reliable 
    because the dependency chart may change its internal structure 
    without warning.
    
    Here is an example of how to access a value from a dependency 
    chart that does not export it:
    
    .. code-block:: yaml
    
         apiVersion: v1
         kind: ConfigMap
         metadata:
           name: myconfigmap
         data:
           redis-host: {{ .Values.redis.primary.host | quote }}

#. Using Library Charts

    Library charts are charts that contain reusable templates and 
    functions that can be shared across multiple charts. You can use 
    library charts to avoid duplicating common templates and logic in 
    your charts.
    
    To use a library chart, you need to add it as a dependency in the 
    `Chart.yaml` file of your chart. Here is an example of how to add 
    a library chart as a dependency:
    
    .. code-block:: yaml
    
         dependencies:
         - name: mylibrary
             version: "1.0.0"
             repository: "https://example.com/charts"
             type: library
    
    After adding the library chart as a dependency, you can use its 
    templates and functions in your chart's templates.                  

#. Importing Values from Child charts using import-values

    You can import values from child charts into the parent chart 
    using the `import-values` field in the `Chart.yaml` file of the 
    parent chart. This allows you to share values between the parent 
    chart and its child charts.
    
    Here is an example of how to import values from a child chart:
    
    .. code-block:: yaml
    
         dependencies:
         - name: mychildchart
             version: "1.0.0"
             repository: "https://example.com/charts"
             import-values:
               - childValue1
               - childValue2
    
    In the parent chart, you can access the imported values using 
    the `{{ .Values.childValue1 }}` and `{{ .Values.childValue2 }}` 
    syntax in your templates.    

#. Importing Values from Child charts using import-values with alias

    You can import values from child charts into the parent chart 
    using the `import-values` field in the `Chart.yaml` file of the 
    parent chart. You can also use the `as` keyword to rename the 
    imported values.
    
    Here is an example of how to import and rename values from a 
    child chart:
    
    .. code-block:: yaml
    
         dependencies:
         - name: mychildchart
             version: "1.0.0"
             repository: "https://example.com/charts"
             import-values:
               - childValue1 as parentValue1
               - childValue2 as parentValue2
    
    In the parent chart, you can access the imported and renamed 
    values using the `{{ .Values.parentValue1 }}` and 
    `{{ .Values.parentValue2 }}` syntax in your templates.

#. Helm Hooks

    Helm hooks are special annotations that allow you to run 
    specific actions at certain points in the lifecycle of a Helm 
    release. Hooks can be used to perform tasks such as database 
    migrations, backups, or cleanup operations.
    
    To create a hook, you need to add the appropriate annotations 
    to your Kubernetes resource definitions in your chart's templates. 
    Here is an example of how to create a pre-install hook:
    
    .. code-block:: yaml
    
         apiVersion: batch/v1
         kind: Job
         metadata:
           name: my-pre-install-job
           annotations:
             "helm.sh/hook": pre-install
             "helm.sh/hook-weight": "0"
             "helm.sh/hook-delete-policy": hook-succeeded
         spec:
           template:
             spec:
               containers:
               - name: my-container
                 image: my-image
                 command: ["my-command"]
               restartPolicy: Never
    
    In this example, the job will run before the chart is installed. 
    The `hook-weight` annotation determines the order in which hooks 
    are executed, and the `hook-delete-policy` annotation specifies 
    when the hook resource should be deleted.

#. Create and use Hooks

    You can create and use hooks in your Helm charts to perform 
    specific actions at certain points in the lifecycle of a Helm 
    release. Hooks can be used for tasks such as database migrations, 
    backups, or cleanup operations.
    
    To create a hook, you need to add the appropriate annotations to 
    your Kubernetes resource definitions in your chart's templates. 
    Here is an example of how to create a post-install hook:
    
    .. code-block:: yaml
    
         apiVersion: batch/v1
         kind: Job
         metadata:
           name: my-post-install-job
           annotations:
             "helm.sh/hook": post-install
             "helm.sh/hook-weight": "0"
             "helm.sh/hook-delete-policy": hook-succeeded
         spec:
           template:
             spec:
               containers:
               - name: my-container
                 image: my-image
                 command: ["my-command"]
               restartPolicy: Never
    
    In this example, the job will run after the chart is installed. 
    The `hook-weight` annotation determines the order in which hooks 
    are executed, and the `hook-delete-policy` annotation specifies 
    when the hook resource should be deleted.

#. Testing Charts with Helm Tests

    Helm provides a built-in mechanism for testing charts using 
    Helm tests. Helm tests are special Kubernetes resources that 
    are defined in the `templates/tests/` directory of your chart. 
    These resources are executed when you run the `helm test` command.
    
    To create a Helm test, you need to define a Kubernetes resource 
    in the `templates/tests/` directory of your chart. Here is an 
    example of how to create a simple test using a Pod:
    
    .. code-block:: yaml
    
         apiVersion: v1
         kind: Pod
         metadata:
           name: my-test-pod
           labels:
             app.kubernetes.io/name: mychart
             app.kubernetes.io/instance: {{ .Release.Name }}
             app.kubernetes.io/version: {{ .Chart.AppVersion }}
             app.kubernetes.io/managed-by: {{ .Release.Service }}
           annotations:
             "helm.sh/hook": test
         spec:
           containers:
           - name: my-container
             image: busybox
             command: ['sh', '-c', 'echo Hello, World! && sleep 30']
           restartPolicy: Never
    
    In this example, the Pod will run a simple command that prints 
    "Hello, World!" and then sleeps for 30 seconds. The `helm.sh/hook` 
    annotation specifies that this resource is a test hook.
    
         
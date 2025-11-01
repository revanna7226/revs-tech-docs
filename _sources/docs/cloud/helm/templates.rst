Templates Deep Dive
==========================

helm uses Go templates to generate Kubernetes manifest files. 
This section provides an in-depth look at the various features and capabilities of Go templates as used in helm charts.

Contents:
    - Template Actions
    - Template Information
    - Template Functions
    - Pipelines
    - Conditionals
    - Loops using range
    - Defining and Using Variables
    - Including and Importing Templates
    - Use with
    - Loop Dictionaries
    - Debugging Templates
    - Helm get manifest
    - _helpers.tpl
    - Create and Use Custom temaplates

#. **Template Actions:**

   Actions are enclosed in {{ ... }} and interpreted by the Go template engine that Helm uses. 
   Everything outside these delimiters is rendered as plain text.

   Handling Whitespace: Helm also provides whitespace control using hyphens,

   - {{- ... }} removes leading whitespace
   - {{ ... -}} removes trailing whitespace
   - {{- ... -}} removes both leading and trailing whitespace

   **Common Actions in Helm Templates:**

   #. **Variable Declarations**
   
       - ``{{ $var := value }}`` — Declare and assign a variable.
       - ``{{ $name := .Values.name }}`` — Access a value from the chart’s `values.yaml`.
       - ``{{ $ := . }}`` — Assign the current context to a variable.

   #. **Conditionals**
   
       - ``{{ if CONDITION }}`` … ``{{ end }}``
       - ``{{ if CONDITION }}...{{ else }}...{{ end }}``
       - ``{{ if and (eq .Values.env "prod") .Values.enabled }}`` — Combine conditions.

   #. **Loops**
   
       - ``{{ range .Values.list }}`` … ``{{ end }}`` — Iterate over a list.
       - ``{{ range $key, $val := .Values.map }}`` — Iterate over key-value pairs.
       - ``{{- range ... -}}`` — Trim whitespace around loop blocks.

   #. **Includes and Templates**
   
       - ``{{ include "template.name" . }}`` — Reuse another template.
       - ``{{ template "template.name" . }}`` — Render a named template.
       - ``{{ define "template.name" }}`` … ``{{ end }}`` — Define a reusable template block.
       - ``{{- include "chart.fullname" . | indent 2 }}`` — Include and indent output.

   #. **Pipelines**
   
       - ``{{ .Values.name | upper }}`` — Pass output through a function.
       - ``{{ .Release.Name | printf "%s-chart" }}`` — Format strings.
       - ``{{ include "template.name" . | nindent 4 }}`` — Indent output with `n` spaces.

   #. **Flow Control and Context**
   
       - ``.`` — Refers to the current context.
       - ``..`` — Refers to the parent context.
       - ``$`` — Root context (top-level scope).

   #. **Comments**
   
       - ``{{/* This is a Helm comment */}}`` — Ignored during rendering.

   #. **Whitespace Control**
   
       - ``{{-`` and ``-}}`` — Trim whitespace from template output.

   #. **Function Calls (Selected Examples)**
   
       - ``{{ default "value" .Values.someVar }}``
       - ``{{ quote .Values.name }}``
       - ``{{ toYaml .Values.config | indent 2 }}``
       - ``{{ required "message" .Values.field }}``
       - ``{{ eq .Values.env "prod" }}``
       - ``{{ ne .Values.env "dev" }}``
       - ``{{ upper "text" }}``
       - ``{{ lower "TEXT" }}``
       - ``{{ trunc 10 .Values.longString }}``

   #. **Release and Chart Metadata**
   
       - ``.Release.Name``
       - ``.Release.Namespace``
       - ``.Chart.Name``
       - ``.Chart.Version``

   #. **Files and Lookups**
   
       - ``{{ .Files.Get "config.yaml" }}`` — Read a file from `charts/` or `files/`.
       - ``{{ .Files.Glob "templates/*.tpl" }}`` — Access multiple files.
       - ``{{ .Files.GetBytes "cert.pem" | b64enc }}`` — Encode binary data.

   **Example:**

   .. code-block:: yaml

      # revschart/templates/deployment.yaml
      apiVersion: apps/v1
      kind: Deployment
         {{.Values.my.custom.data | nindent 2}} # Indent custom data by 2 spaces -> RevsMyName
         {{.Chart.Name}} # Access chart name -> revschart
         {{.Chart.Version}} # Access chart version -> 0.1.0
         {{.Chart.AppVersion}} # Access chart app version -> 1.16.0
         {{.Chart.Annotations}} # Access chart annotations -> map[]
         {{.Release.Name}} # Access release name -> release-name
         {{.Release.Namespace}} # Access release namespace -> default
         {{.Release.IsInstall}} # Check if install -> true
         {{.Release.IsUpgrade}} # Check if upgrade -> false
         {{.Release.Service}} # Access release service -> Helm
         {{.Template.Name}} # Access template name -> revschart/templates/deployment.yaml
         {{.Template.BasePath}} # Access template base path -> revschart/templates
         {{.Values.my.custom.data2 | default "None" | upper | quote}} # Default, upper, quote -> "NONE"




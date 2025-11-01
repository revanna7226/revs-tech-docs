Repository
==================

Local Repository

    #. Setup Local Repository

        .. code-block:: bash
            
            helm repo index chartsrepo/

            helm package revschart -d ../chartsrepo/

    #. Host Repository

    - You can host the `chartsrepo/` directory on any web server or cloud storage that serves static files.
    - For example, you can use GitHub Pages, Amazon S3, or any web hosting service.

        .. code-block:: bash
        
        # Example Local Hosting with Python HTTP Server
        cd chartsrepo/
        python3 -m http.server --bind 127.0.0.1 8080 --directory .
        # Now your repository is accessible at http://127.0.0.1:8080/

    #. Add Repository to Helm

        .. code-block:: bash
        
        helm repo add revsrepo http://127.0.0.1:8080/

    #. Update Repository

        .. code-block:: bash
        
        helm repo update
        # Regenerate index if you add new charts
        helm repo index chartsrepo/ 

    #. Install Chart from Repository

        .. code-block:: bash
        
        helm install revschart revsrepo/revschart


    #. Verify Installation

        .. code-block:: bash
        
        helm list

    #. Helm Search Repository

        .. code-block:: bash
        
        helm search repo revschart

GitHub Repository

    #. Setup GitHub Repository
        - Create a new GitHub repository (e.g., `gitchartrepo`).
        - Clone the repository locally.

        .. code-block:: bash
        
        git clone https://github.com/revanna7226/gitchartrepo.git

    #. Go to the cloned directory

        .. code-block:: bash
        
            cd gitchartrepo/

            helm create demochart
            helm package demochart -d .
            helm repo index .

    #. Push to GitHub   

        .. code-block:: bash
        
            git add .
            git commit -m "Add Helm chart and index"
            git push origin main

    #. Host GitHub Pages
        - Go to the repository settings on GitHub.
        - Under the "Pages" section, set the source to the `main` branch and the root directory (`/`).
        - Save the settings. Your repository will be available at `https://<your-username>.github.io/gitchartrepo/`.

    #. Add GitHub Repository to Helm

        .. code-block:: bash
        
        helm repo add gitrepo https://revanna7226.github.io/gitchartrepo/
        helm repo list
        helm search repo demochart
        helm install gitapp gitrepo/demochart
        
    #. Verify Installation  

        .. code-block:: bash
        
        helm list

#. OCI Registries

    #. Push Chart to OCI Registry

        .. code-block:: bash
        
        helm chart save revschart oci://my-oci-registry/revscharts:0.1.0
        helm chart push oci://my-oci-registry/revscharts:0.1.0

    #. Pull Chart from OCI Registry

        .. code-block:: bash
        
        helm chart pull oci://my-oci-registry/revscharts:0.1.0
        helm chart export oci://my-oci-registry/revscharts:0.1.0 --destination ./charts

    #. Install Chart from OCI Registry

        .. code-block:: bash
        
        helm install ociapp oci://my-oci-registry/revscharts --version 0.1.0

    #. Verify Installation

        .. code-block:: bash
        
        helm list

# . Clean Up

    #. Uninstall Helm Releases

        .. code-block:: bash
        
        helm uninstall revschart
        helm uninstall gitapp
        helm uninstall ociapp

    #. Remove Repositories

        .. code-block:: bash
        
        helm repo remove revsrepo
        helm repo remove gitrepo
        helm repo remove ocirepo

    #. Delete Local Files

        .. code-block:: bash
        
        rm -rf chartsrepo/
        rm -rf gitchartrepo/
        rm -rf charts/

#. Summary

    - You have successfully created and managed Helm chart repositories using local hosting, GitHub Pages, and OCI registries.
    - You can now package, distribute, and deploy Helm charts efficiently using these methods.

Course Commands:

.. code-block:: bash

    docker run -d --name oci-registry -p 5000:5000 registry

    helm package firstchart

    helm push firstchart-0.1.0.tgz oci://localhost:5000/helm-charts

    helm show all oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm pull oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm template myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm install myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm upgrade myrelease oci://localhost:5000/helm-charts/firstchart --version 0.2.0

    helm registry login -u myuser <oci registry>

    helm registry logout <oci registry url>



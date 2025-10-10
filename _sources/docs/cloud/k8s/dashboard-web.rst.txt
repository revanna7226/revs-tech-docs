Kubernetes Dashboard
========================

To launch the Kubernetes Dashboard UI when Kubernetes is enabled through Docker Desktop, follow these steps:

#. Enable Kubernetes in Docker Desktop:

   - Open Docker Desktop.
   - Go to Settings → Kubernetes.
   - Check “Enable Kubernetes” and apply the changes.
   - Wait until Kubernetes starts and is running (you will see the Kubernetes icon).


#. Deploy the Kubernetes Dashboard:

   - The dashboard is not installed by default. 
   - Use this command to deploy the official Kubernetes Dashboard in your cluster:

   .. code-block:: bash
      
      kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

#. Create a Service Account and ClusterRoleBinding for Dashboard Access:
   Create an admin user so you can log in with a token.

   dashboard-admin.yaml example:

   .. code-block:: yaml
      
      apiVersion: v1
      kind: ServiceAccount
      metadata:
         name: dashboard-admin
         namespace: kubernetes-dashboard
      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
         name: dashboard-admin
      roleRef:
         apiGroup: rbac.authorization.k8s.io
         kind: ClusterRole
         name: cluster-admin
      subjects:
         - kind: ServiceAccount
           name: dashboard-admin
           namespace: kubernetes-dashboard
         
   Apply it with:

   .. code-block:: bash
      
      kubectl apply -f dashboard-admin.yaml

#. Get the Bearer Token for Login:

   Run this command to get the login token:

   .. code-block:: bash

      kubectl -n kubernetes-dashboard create token dashboard-admin

   Copy the token output.

#. Access the Dashboard:
   
   Start the kubectl proxy:

   .. code-block:: bash
      
      kubectl proxy

      #>> error: listen tcp 127.0.0.1:8001: bind: address already in use
    
      # port 8001 on your Ubuntu system is currently being used by another process
      # The command below correctly lists network processes listening on port 8001.
      sudo ss -ltnp 'sport = :8001'

      # Kill the process using its PID (942) with:
      kill 942
      kill -9 942 # Or force kill if needed

      # If the process no longer appears, port 8001 is now free.
      sudo ss -ltnp 'sport = :8001'

      # Restart kubectl proxy
      kubectl proxy
   
   Then open this URL in your browser:

   .. admonition:: Note
      
      http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

#. Login:
   
   Use the copied token to log in to the Kubernetes Dashboard UI.
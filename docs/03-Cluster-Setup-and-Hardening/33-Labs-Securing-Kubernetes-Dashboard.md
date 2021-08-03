# Lab - Security Kubernetes Dashboard
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-secure-kubernetes-dashboard/)

Solutions to Lab Security Kubernetes Dashboard:

- **`Kubernetes Dashboard`** is a Web UI tool for managing kubernetes cluster

- Install kubernetes dashboard following the installing guides in the kubernetes documentation site.

  <details>

  ```
  $ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
  ```
  </details>

- Now run kubectl proxy with the command kubectl proxy --address=0.0.0.0 --disable-filter &

    Click on Kubernetes API on top left to open a new tab and you will see API endpoints.

    Append /api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login to URL to check login UI

    Note: From a security perspective do not use --disable-filter option as it can leave you vulnerable to XSRF attacks, when used with an accessible port. We have used this option to make our lab environment work with the kubernetes dashboard so you can access it through a browser. Ideally you would be accessing it through a kubectl proxy on your localhost only. So in actual environments do not use --disable-filter option as its a major security risk

  <details>

  ```
  OK
  ```
  </details>

- `Username and Password` is not a way of logging into Kubernetes dashboard.

- **`The cluster-admin ClusterRole`**  provides access to entire cluster.

- Get the token of admin-user and login into the UI. Check whether you can modify any of resources
Get token by running below and login to the UI with that token.

  <details>

  ```
  $ kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"

  OK
  ```
  </details>

- What namespaces can you see from the UI?  
  <details>
  
  ```
  All of the above
  ```
  </details>

- What secrets can you see under the kube-system namespace from the UI?

  <details>
  
  ```
  All of the above
  ```
  </details>

- Are you able to create new resources from the UI?
Use the plus button at the top right corner of the screen and paste a sample YAML file to create a pod.
  <details>
  
  ```
  Yes
  ```
  </details>

- As you can see the admin-user is way too powerful. Let's now create a new Service Account user readonly-user in the kubernetes-dashboard namespace with view permissions to all resources in all namespaces
Clusterrole name : view
ClusterRoleBinding name :readonly-user-binding
  <details>
  
  ```

      cat <<EOF | kubectl apply -f -

      apiVersion: v1

      kind: ServiceAccount

      metadata:

        name: readonly-user

        namespace: kubernetes-dashboard

      EOF

      cat <<EOF | kubectl apply -f -

      apiVersion: rbac.authorization.k8s.io/v1

      kind: ClusterRoleBinding

      metadata:

        name: readonly-user-binding

      roleRef:

        apiGroup: rbac.authorization.k8s.io

        kind: ClusterRole

        name: view

      subjects:

      - kind: ServiceAccount

        name: readonly-user

        namespace: kubernetes-dashboard

      EOF

  ```
  </details>

- Logout the admin-user from the Kubernetes Dashboard UI. Get the token of readonly-user and login into the UI.
Check whether you can modify any of the resources.
Run below command to get token
$ kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/readonly-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
  <details>
  
  ```
  OK
  ```
  </details>

- Are you able to delete any resource from the UI?
Click on the button (3 dots) to the right of each object and select delete. Click on confirm to see if it can be deleted.
  <details>

  ```
  No
  ```
  </details>

- The read-only user has too few privileges. Now let's create a dashboard-admin user with access to the kubernetes-dashboard namespace only.
Use admin ClusterRole with RoleBinding so that it gives full control over every resource in the role binding's namespace, including the namespace itself.
Also assign list-namespace ClusterRole to only see namespaces.
Please use below names for bindings
RoleBinding name: dashboard-admin-binding
ClusterRoleBinding name: dashboard-admin-list-namespace-binding

  <details>

  ```

      cat <<EOF | kubectl apply -f -
      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: dashboard-admin
        namespace: kubernetes-dashboard
      EOF
      # admin RoleBinding
      cat <<EOF | kubectl apply -f -
      apiVersion: rbac.authorization.k8s.io/v1
      kind: RoleBinding
      metadata:
        name: dashboard-admin-binding
        namespace: kubernetes-dashboard
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: admin
      subjects:
      - kind: ServiceAccount
        name: dashboard-admin
        namespace: kubernetes-dashboard
      EOF

      ## list-namespace ClusterRoleBinding

      cat <<EOF | kubectl apply -f -
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
        name: dashboard-admin-list-namespace-binding
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: list-namespace
      subjects:
      - kind: ServiceAccount
        name: dashboard-admin
        namespace: kubernetes-dashboard
      EOF
   ```
  </details>

- Get the token of dashboard-admin and login into the UI. Confirm only dashboard-admin namespace resources can be updated with dashboard-admin user.

  In the UI namespace, dropdown and select kubernetes-dashboard namespace to see its resources. Confirm that other namespace resources are not visible to this user.
  
- Run below command to get token
  ```
  $ kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/dashboard-admin -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
    
  ```
  <details>

  ```
  OK
  ```
  </details>




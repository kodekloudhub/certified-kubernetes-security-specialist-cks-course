# Cluster-Roles-and-Role-Bindings

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/cluster-roles-and-role-bindings/)

In this section, we will take a look at `Cluster-Roles-and-Role-Bindings`.

## Roles
- Roles and Rolebindings are namespaced means they are created within namespaces.

  ![roles](../../images/roles.PNG)

## Namespaces
- Can you group or isolate nodes within a namespace?
  - No, those are cluster wide or cluster scoped resources. They cannot be associated with any particular namespace.

  ![namespace](../../images/namespace.PNG)

- So the resources are categorized as either namespaced or cluster scoped.

- To see namespaced resources
  ```
  $ kubectl api-resources --namespaced=true
  ```
- To see non-namespaced resources
  ```
  $ kubectl api-resources --namespaced=false
  ```

  ![namespace1](../../images/namespace1.PNG)

## Cluster Roles and Cluster Role Bindings
- Cluster Roles are roles except they are for a cluster scoped resource. So kind as **`ClusterRole`**
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    name: cluster-administrator
  rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["nodes"]
    verbs: ["get", "list", "delete", "create"]
  ```
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
    name: cluster-admin-role-binding
  subjects:
  - kind: User
    name: cluster-admin
    apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: ClusterRole
    name: cluster-administrator
    apiGroup: rbac.authorization.k8s.io
  ```
  ```
  $ kubectl create -f cluster-admin-role.yaml
  $ kubectl create -f cluster-admin-role-binding.yaml
  ```

 ![cr1](../../images/cr1.PNG)

- You can create a cluster role for namespace resources as well. When you do that, the user will have access to these resources across all namespaces.

#### K8s Reference Docs

- https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-and-clusterrole
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/#command-line-utilities



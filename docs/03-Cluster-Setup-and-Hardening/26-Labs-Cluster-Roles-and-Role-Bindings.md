# Lab - Cluster-Roles-and-Role-Bindings

  - Take me to the [Lab](https://kodekloud.com/topic/labs-cluster-roles-and-role-bindings/)

Solutions for Lab - ClusterRoles and RoleBindings:

- Run the command kubectl get clusterroles --no-headers | wc -l or kubectl get clusterroles --no-headers -o json | jq '.items | length'

  <details>

  ```sh
  $ kubectl get clusterroles --no-headers | wc -l 
  OR
  $ kubectl get clusterroles --no-headers -o json | jq '.items | length'
  ```
  </details>

- Run the command kubectl get clusterrolebindings --no-headers | wc -l or kubectl get clusterrolebindings --no-headers -o json | jq '.items | length'

  <details>

  ```sh
  $ kubectl get clusterrolebindings --no-headers | wc -l 
  OR
  $ kubectl get clusterrolebindings --no-headers -o json | jq '.items | length'
  ```
  </details>

- What namespace is the cluster-admin clusterrole part of?

  <details>

  ```
  $ Cluster roles are cluster wide and not part of any namespace
  ```
  </details>

- Run the command kubectl describe clusterrolebinding cluster-admin

  <details>

  ```
  $ kubectl describe clusterrolebinding cluster-admin
  ```
  </details>

- Run the command kubectl describe clusterrole cluster-admin

  <details>

  ```
  $ kubectl describe clusterrole cluster-admin
  ```
  </details>

- Check answer at /var/answers

  <details>

  ```
  $ kubectl create -f /var/answers/michelle-node-admin.yaml
  ```
  </details>

- Check answer at /var/answers

  <details>

  ```
  $ kubectl create -f /var/answers/michelle-storage-admin.yaml
  ```
  </details>



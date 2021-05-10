# Labs Admission Controllers
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704473)

Solutions Labs Admission Controllers

# Labs Admission Controllers
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704473)

Solutions Labs Admission Controllers

- **`authenticate user`** is not a function of admission controller

- **`NamespaceAutoProvision`** is an admission controller that is not enabled by deafult

- **`NodeRestriction`** is an enabled admission controller in this cluster and is normally disabled

- Run below command to deploy a pod with the nginx image in the blue namespace

  <details>
  ```
  $ kubectl run nginx --image nginx -n blue
  ```
  </details>

- The previous step failed because kubernetes have NamespaceExists admission controller enabled

  <details>
  ```
  Add NamespaceAutoProvision admission controller to --enable-admission-plugins list to /etc/kubernetes/manifests/kube-apiserver.yaml
  It should be like

      - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision

  ```
  </details>

- Run the below command to deploy single pod with nginx image in blue namespace

  <details>
  ```
  $ kubectl run nginx --image nginx -n blue
  ```
  </details>

- Disable DefaultStorageClass admission controller

  <details>
  ```
  Update /etc/kubernetes/manifests/kube-apiserver.yaml as below
     - --disable-admission-plugins=DefaultStorageClass
  ```
  </details>

- Check the process to see enabled and disabled plugins.

  <details>
  ```
  $ ps -ef | grep kube-apiserver | grep admission-plugins
  ```
  </details>

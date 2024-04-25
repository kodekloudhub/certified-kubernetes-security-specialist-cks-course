# Lab - OPA in Kubernetes
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-opa-in-kubernetes/)

Solutions for Lab - OPA in Kubernetes:

- **`Manage kubernetes objects via OPA`** is not a function of Kube-mgmt.

- **`Create configmaps on Kubernetes with the label openpolicyagent.org/policy set to rego`** needs to be done to enable kube-mgmt to automatically identify policies defined in kubernetes and load them into OPA.

- We have placed rego policies under `/root/untrusted-registry.rego` and `/root/unique-host.rego`. View the contents of these files and identify which kubernetes resources will be validated by these rego policies?
  <details>

  ```
  By analyzing the contents of /root/untrusted-registry.rego and /root/unique-host.rego
  Answer:
  untrusted-registry.rego : pod ; unique-host.rego : ingress
  ```
  </details>

 - If we were to implement the policy under `/root/untrusted-registry.rego` and create a pod as defined in `/root/test.yaml`, which of the 2 containers will error out?
  <details>
  
  ```
  untrusted-registry.rego policy denies pods with image name that does not start with hooli.com/
  Answer:
  nginx-frontend
  ```
  </details>

- Create a configmap for OPA using the untrusted-registry.rego policy
Use below files:
configmap file: /root/untrusted-registry.rego
configmap name : untrusted-registry
  <details>

  ```
  Run
  $ kubectl create configmap untrusted-registry --from-file=untrusted-registry.rego
  ```
  </details>

- Create a pod defined under `/root/test.yaml` in the namespace dev. Fix the OPA validation issue while creating the pod.
  <details>

  ```
  Run
  $ kubectl apply -n dev -f /root/test.yaml
  $ vi /root/test.yaml
  Correct the file as below
  kind: Pod
  apiVersion: v1
  metadata:
   name: test
  spec:
   containers:
   - image: hooli.com/nginx
      name: nginx-frontend
   - image: hooli.com/mysql
      name: mysql-backend
  Run
  $ kubectl apply -n dev -f /root/test.yaml
  ```
  </details>

- As per policy in `/root/unique-host.rego`, which ingress resources will be denied for creation?
  <details>

  ```
  Run
  $ /root/unique-host.rego
  Check the Ingress object
  Answer:
  multiple ingress resources with same host
  ```
  </details>

- Create a configmap named unique-host using the rego file `/root/unique-host.rego` for OPA.
  <details>

  ```
  Run
  $ kubectl create configmap unique-host --from-file=/root/unique-host.rego
  ```
  </details>

- Create 2 ingress resources using below files
/root/ingress-test-1.yaml
/root/ingress-test-2.yaml
Check if you can create both resources
  <details>
    
  ```
  Run
  $ kubectl apply -f /root/ingress-test-1.yaml
  $ kubectl apply -f /root/ingress-test-2.yaml
  You can't create both of them due to conflicts
  ```
  </details>

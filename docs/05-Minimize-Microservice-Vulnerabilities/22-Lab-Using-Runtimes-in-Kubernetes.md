# Lab - Using Runtimes in Kubernetes
  
  - Take me to the [Lab](https://kodekloud.com/topic/lab-using-runtimes-in-kubernetes/)

Solutions for Lab - Using Runtimes in Kubernetes:

- **`runc`** is the default runtime used by this cluster.

- Inspect the runtime classes in this cluster to answer the next questions
  <details>

  ```
  Answer:
  OK
  ```
  </details>

- **`runsc`**  is the handler used by the runtime class called gvisor
  <details>
    
  ```
  Run

  $ kubectl describe runtimeclasses gvisor  | grep Handler

  ```
  </details>

- **`Kata-Containers`** runtimeclass object makes use of kata-runtime as the handler?


- To create a new runtime class, Run
  <details>
  
  ```
  $ vi runtime.yaml

  use the following yaml file
  apiVersion: node.k8s.io/v1
  kind: RuntimeClass
  metadata:
    name: secure-runtime
  handler: runsc

  $ kubectl apply -f runtime.yaml
  ```   
  </details>

- A pod definition file is provided at /root/simple-webapp-1.yaml. Update this file with the runtime class that we just created in the previous step. runtimeClassName: secure-runtime
  <details>
  
  ```
  $ vi /root/simple-webapp-1.yaml

  use the following yaml file

  apiVersion: v1
  kind: Pod
  metadata:
    name: simple-webapp-1
    labels:
      name: simple-webapp
  spec:
    runtimeClassName: secure-runtime
    containers:
      - name: simple-webapp
        image: kodekloud/webapp-delayed-start
        ports:
        - containerPort: 8080

  $ kubectl apply -f /root/simple-webapp-1.yaml
  ```
  </details> 
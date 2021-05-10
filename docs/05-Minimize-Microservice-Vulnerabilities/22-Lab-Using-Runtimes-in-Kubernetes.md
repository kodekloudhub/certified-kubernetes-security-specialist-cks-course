# Labs Using Runtimes in Kubernetes
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704730)

Solutions for Labs Using Runtimes in Kubernetes

- Which is the default runtime used by this cluster
  <details>
  ```
  This kubernetes cluster uses docker as the runtime. Docker makes use of runc to start containers
  Answer:
  runc
  ```
  </details>

- Inspect the runtime classes in this cluster to answer the next questions
  <details>
  ```
  Answer:
  OK
  ```
  </details>

- What is the handler used by the runtime class called gvisor
  <details>
  ```
  Run
  $ kubectl describe runtimeclasses gvisor  | grep Handler

  Answer: runsc
  ```
  </details>

- Which runtimeclass object makes use of kata-runtime as the handler?
  <details>
  ```
  Run
  $ kubectl describe runtimeclasses kata-containers | grep Handler

  Answer:
  Kata-Containers

  ```
  </details>

- Create a new runtime class called secure-runtime with the following specs:
Name: secure-runtime
Handler: runsc

  $ vi runtime.yaml
  use the following yaml file
      apiVersion: node.k8s.io/v1
      kind: RuntimeClass
      metadata:
        name: secure-runtime
      handler: runsc
  $ kubectl apply -f runtime.yaml


- A pod definition file is provided at /root/simple-webapp-1.yaml. Update this file with the runtime class that we just created in the previous step. runtimeClassName: secure-runtime

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

# Using Runtimes in Kubernetes
  - Take me to [Video Tutoral](https://kodekloud.com/courses/1378608/lectures/31704729)

In this section, we will take a look at Using Runtimes in Kubernetes

### How to  instruct the pods to specifically make use of the gvisor runtime?

  gvisor.yaml

    apiVersion: node.k8s.io/v1  
    kind: RuntimeClass
    metadata:
      name: gvisor  
    handler: runsc


  **`kubectl create -f gvisor.yml`**

Specify a runtimeClassName in the Pod spec:

  gvisor-nginx.yaml

    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      runtimeClassName: gvisor
      containers:
      - image: nginx
        name: nginx


  **`kubectl create -f gvisor-nginx.yaml`**

  ![gvisor-k8s.png](../../images/gvisor-k8s.png)

# Lab - PSP
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-psp/)

Solutions to Lab - PSP:

- By analyzing **`/root/pod.yaml`**, the security risks are in that pod are **`All of the above`**.

- Create a pod using the file `/root/pod.yaml`.

  <details>

  ```
  Run
  $ kubectl apply -f /root/pod.yaml
  ```
  </details>

- What is the state of the PodSecurityPolicy admission controller plugin now?

  <details>

  ```
  Run
  $ kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'admission-plugins'
  Disabled

  ```
  </details>

- Enable PodSecurityPolicy in Kubernetes API server

  <details>

  ```
  $ vi /etc/kubernetes/manifests/kube-apiserver.yaml
  Add PodSecurityPolicy admission controller to --enable-admission-plugins list like below
  - --enable-admission-plugins=NodeRestriction,PodSecurityPolicy
  No need for apply or restart

  ```
  </details>

- Which of the following statements are incorrect for the policy defined in `/root/psp.yaml`?

  <details>

  ```
  Allows pod to run only if they have some capabilities defined

  ```
  </details>

- Deploy policy under `/root/psp.yaml`

  <details>

  ```
  Run
  $ kubectl apply -f /root/psp.yaml  
  ```
  </details>

- Delete the pod from `/root/pod.yaml` and try to create again

  <details>

  ```
  Run
  $ kubectl delete -f /root/pod.yaml
  $ kubectl apply -f /root/pod.yaml
  you cannot create same pod after PodSecurityPolicy is enabled and policies are added
  ```
  </details>

- Fix `/root/pod.yaml` for security policies and try to create it again

  <details>

  ```
  $ vi /root/pod.yaml
  Edit the file, it should looks like below:

  apiVersion: v1
  kind: Pod
  metadata:
      name: example-app
  spec:
      containers:
          -
              name: simple-webapp
              image: ubuntu
              command: ["sleep" , "3600"]
              securityContext:
                privileged: false
                runAsUser: 0
      volumes:
      -   name: data-volume
          hostPath:
            path: '/data'
            type: Directory
  ```
  </details>

- Which of the below statement is incorrect for below security policies:

  requiredDropCapabilities:
  - 'CAP_SYS_BOOT'
  defaultAddCapabilities:
  - 'CAP_SYS_TIME'
  runAsUser:
    rule: 'MustRunAsNonRoot'

  <details>

  ```
  CAP_SYS_BOOT capability will be ignored if specified in pod definition and pod will be created
  ```
  </details>

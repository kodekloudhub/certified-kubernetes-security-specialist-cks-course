# Lab Manage Kubernetes Secrets
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31808498)

Solutions for Lab Manage Kubernetes Secrets

- To get the Secrets exist on the system, Run
  <details>
  ```
  Run
  $ kubectl get secrets
  Answer:
  1
  ```
  </details>

- How many secrets are defined in the default-token secret
  <details>
  ```
  Get the name of the secret by running
  $ kubectl get secrets
  Then look at the data field using
  $ kubectl describe secrets default-token-sphlt
  Answer:
  3
  There are three secrets - ca.crt, namespace and token.
  ```
  </details>
- **`kubernetes.io/service-account-token`** is the type of the default-token secret?
  <details>
  ```
  Look at the Type field using

  $ kubectl describe secrets default-token-sphlt  
  ```
  </details>
- **`Type`** is not a secret data defined in default-token secret


-  Create a new secret named db-secret.
  <details>
  ```

  Run
  $ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
  ```
  </details>
-  Configure webapp-pod to load environment variables from the newly created secret.
  <details>
    ```

    Run
    $ kubectl delete pod webapp-pod

    $ vi pod.yaml

        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            name: webapp-pod
          name: webapp-pod
          namespace: default
        spec:
          containers:
          - image: kodekloud/simple-webapp-mysql
            imagePullPolicy: Always
            name: webapp
            envFrom:
            - secretRef:
                name: db-secret
          $ kubectl apply -f pod.yaml
          ```
      </details>

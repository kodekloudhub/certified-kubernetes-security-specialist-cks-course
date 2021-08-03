# Lab - Service Accounts

  - Take me to the [Lab](https://kodekloud.com/topic/lab-service-accounts/)

Solutions for lab - Service Accounts:-

- There is **`1`** service account in the default namespace.

  ```
  Run the command 'kubectl get serviceaccounts' and count the number of accounts.
  ```
  
- The **`default-token-bxx92`** is the secret token used by the default service account.

  ```
  Run the command 'kubectl describe serviceaccount default' and look at the token field.
  ```
  
- The state of the dashboard **`failed`**.

- The **`Service Account`** is the type of account does the Dashboard application use to query the Kubernetes API.

- Which account does the Dashboard application use to query the Kubernetes API? **`Default`**

- **`/var/run/secrets`** is the location of the ServiceAccount credentials available within the pod.

  ```
  Run the command 'kubectl describe pod' and look for volume mount path.
  ```
  
- The command **`kubectl create serviceaccount dashboard-sa`** is used to create a new ServiceAccount named 'dashboard-sa'.


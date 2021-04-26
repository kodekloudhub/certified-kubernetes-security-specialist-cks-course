# Labs Service-Accounts
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31703385)


  - There is **`1`** Service Account in the default namespace.

        Run the command 'kubectl get serviceaccounts' and count the number of accounts.

  - The **`default-token-bxx92`** is the the secret token used by the default service account

        Run the command 'kubectl describe serviceaccount default' and look at the token field

  - the state of the dashboard **`failed`**

  - the **`Service Account`** is the type of account does the Dashboard application use to query the Kubernetes API

  - Which account does the Dashboard application use to query the Kubernetes API? **`Default`**

  - **`/var/run/secrets`** is the location of the ServiceAccount credentials available within the pod.

        Run command 'kubectl describe pod' and look for volume mount path

  - The command **`kubectl create serviceaccount dashboard-sa`**is used to create a new ServiceAccount named 'dashboard-sa'.

# KubeConfig

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/kubeconfig-2/)

In this section, we will take a look at `KubeConfig`.

#### Client uses the certificate file and key to query the kubernetes Rest API for a list of pods using curl.
- You can specify the same using kubectl

  ![kc1](../../images/kc1.PNG)

- We can move this information to a configuration file called kubeconfig. And then specify this file as the kubeconfig option in the command.
  ```
  $ kubectl get pods --kubeconfig config
  ```

## Kubeconfig File
- The kubeconfig file has 3 sections
  - Clusters
  - Contexts
  - Users

  ![kc4](../../images/kc4.PNG)

  ![kc5](../../images/kc5.PNG)

- To view the current file being used
  ```
  $ kubectl config view
  ```
- You can specify the kubeconfig file with kubectl config view with "--kubeconfig" flag
  ```
  $ kubectl config veiw --kubeconfig=my-custom-config
  ```

  ![kc6](../../images/kc6.PNG)

- How do you update your current context? Or change the current context
  ```
  $ kubectl config view --kubeconfig=my-custom-config
  ```

  ![kc7](../../images/kc7.PNG)

- kubectl config help
  ```
  $ kubectl config -h
  ```

  ![kc8](../../images/kc8.PNG)

## What about namespaces?

  ![kc9](../../images/kc9.PNG)

## Certificates in kubeconfig

  ![kc10](../../images/kc10.PNG)

  ![kc12](../../images/kc12.PNG)

  ![kc11](../../images/kc11.PNG)

#### K8s Reference Docs

- https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
- https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

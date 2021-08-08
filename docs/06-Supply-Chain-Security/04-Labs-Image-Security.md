# Lab - Image Security
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-image-security/)

Solutions Lab - Image Security:

- The **`nginx:alpine`** image is the application using.


- Use the `kubectl edit deployment` command to edit the image name to `myprivateregistry.com:5000/nginx:alpine`.

  <details>

  ```
  $ kubectl edit deployment web
  ```

  </details>

- Run the command `kubectl get pods` command and check the status of the pods.

  <details>

  ```
  $ kubectl get pods
  ```

  </details>

- Create a secret object with the credentials required to access the registry.

  <details>

  ```

  $ kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com
  ```
  </details>

- Configure the deployment to use credentials from the new secret to pull images from the private registry

  <details>

  ```
  $ Edit deployment using kubectl edit deploy web command and add imagePullSecrets section. Use private-reg-cred
  ```

  </details>

- To Check the status of PODs, Run

  <details>

  ```
  $ kubectl get pods
  ```
  </details>

# Labs Image Security
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704747)

Solutions Labs Image Security

- We have an application running on our cluster. Let us explore it first. What image is the application using?

  <details>

  ```
  Run

  $ kubectl describe deployments  web

  and check Image

  Answer: nginx:alpine
  ```

  </details>

- Use the kubectl edit deployment command to edit the image name to myprivateregistry.com:5000/nginx:alpine

  <details>

  ```
  $ kubectl edit deployment web
  ```

  </details>

- Run the command kubectl get pods command and check the status of the pods

  <details>

  ```
  $ kubectl get pods
  ```

  </details>

- Create a secret object with the credentials required to access the registry

Name: private-reg-cred

Username: dock_user

Password: dock_password

Server: myprivateregistry.com:5000

Email: dock_user@myprivateregistry.com


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

- Check the status of PODs. Wait for them to be running. You have now successfully configured a Deployment to pull images from the private registry

  <details>

  ```
  $ kubectl get pods
  ```
  </details>

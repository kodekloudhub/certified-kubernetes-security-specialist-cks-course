# Lab - Whitelist Allowed Registries Image Policy Webhook
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-whitelist-allowed-registries-imagepolicywebhook/)

Solutions to Lab - Whitelist Allowed Registries Image Policy Webhook:

- For this lab we are going to deploy a ImagePolicyWebhook which will deny us from using images with latest tag and ensure that all images have tags.
  <details>

  ```
  OK
  ```
  </details>

- Deploy the ReplicaSet defined in the file /root/nginx-latest.yml and validate that the pod is running.
This YAML file uses the latest nginx image from DockerHub.
  <details>

  ```
  Run

  $ kubectl apply -f /root/nginx-latest.yml

  $ kubectl get rs

  $ kubectl get po -l tier=nginx-latest
  ```
  </details>

- Deploy an Image Policy Webhook server.
  <details>

  ```
  Run

  $ kubectl apply -f image-policy-webhook.yaml

  ```
  </details>

- **`docker.io`** is the value passed to registry-whitelist parameter by the command running inside the webhook pods
  <details>

  ```
  Get webhook pods by

  $ kubectl get pods | grep webhook

  Check the value of the flag --registry-whitelist in

  $ kubectl describe po <image-bouncer-webhook-POD>

  ```
</details>


- Fix those two YAML files so that it works with ImagePolicyWebhook.

  <details>

  ```
  $ vi /etc/kubernetes/pki/admission_configuration.yaml

  Add the path to the kubeconfig file:

  kubeConfigFile: /etc/kubernetes/pki/admission_kube_config.yaml

  Check webhook service by running:

  $ kubectl describe svc image-bouncer-webhook

  $ vi /etc/kubernetes/pki/admission_kube_config.yaml

  Edit this line:

  server: https://image-bouncer-webhook:30080/image_policy

  You can verify by running

  $ netstat -na | grep 30080

  Or

  $ telnet image-bouncer-webhook 30080

  ```
  </details>

- Enable the ImagePolicyWebhook admission controller as final step so that our image policy validation can take place in API server.
  <details>

  ```
  $ vi /etc/kubernetes/manifests/kube-apiserver.yaml

  Edit those lines:

  - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook

  - --admission-control-config-file=/etc/kubernetes/pki/admission_configuration.yaml
  ```
  </details>

- Now delete and create pod in `/root/nginx-latest.yml` and validate that there is an error reported when we use the latest image tag.
  <details>

  ```
  Run
  $ kubectl delete -f /root/nginx-latest.yml
  $ kubectl apply -f /root/nginx-latest.yml
  Note:
  You should see an error message when you run:
  $ kubectl describe replicaset nginx-latest
  ```
  </details>

- Fix the error for untrusted registry in `/root/nginx-latest.yml` using the `1.19` image.
  <details>

  ```
  $ vi /root/nginx-latest.yml
  # Edit the line below:
       image: nginx:1.19

  $ kubectl apply -f /root/nginx-untrusted.yml
  
  # Check the pod created by running:
  $ kubectl describe replicaset nginx-latest
  
  $ kubectl get pod | grep nginx-latest
  ```
  </details>

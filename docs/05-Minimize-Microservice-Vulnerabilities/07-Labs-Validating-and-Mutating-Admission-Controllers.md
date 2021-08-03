# Lab - Validating and Mutating Admission Controllers
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-validating-and-mutating-admission-controllers/)

Solutions Lab - Validating and Mutating Admission Controllers:

- **`NamespaceAutoProvision- Mutating , NamespaceExists - Validating`** is correct for Mutating and validating admission controllers.

- **`First Mutating then Validating`** is the flow of invocation of admission controllers.

- Create a namespace webhook-demo where we will deploy webhook components.

  <details>

  ```
  Run
  $ kubectl create ns webhook-demo
  ```
  </details>

- Create TLS secret webhook-server-tls for secure webhook communication in webhook-demo namespace.

  <details>

  ```
  Run
  $ kubectl -n webhook-demo create secret tls webhook-server-tls \
    --cert "/root/keys/webhook-server-tls.crt" \
    --key "/root/keys/webhook-server-tls.key"
  ```
  </details>

- Create a webhook deployment

  <details>

  ```
  Run
  $ kubectl create -f /root/webhook-deployment.yaml
  ```
  </details>

- Create a webhook service

  <details>

  ```
  Run
  $ kubectl create -f /root/webhook-service.yaml
  ```
  </details>

- If we apply the configuration by adding MutatingWebhookConfiguration, which resources and actions would be affected

  <details>

  ```
  Pod with CREATE operations
  ```
  </details>

- Deploy MutatingWebhookConfiguration

  <details>

  ```
  Run
  $ kubectl create -f /root/webhook-configuration.yaml
  ```
  </details>

- Deploy a pod with no securityContext specified.

  <details>

  ```
  Run
  $ kubectl apply -f /root/pod-with-defaults.yaml
  ```
  </details>

- What are runAsNonRoot and runAsUser values for previously created pods securityContext?

  <details>

  ```
  runAsNonRoot: true , runAsUser: 1234
  ```
  </details>

- Deploy pod with a securityContext explicitly allowing it to run as root.

  <details>

  ```
  Run
  $ kubectl apply -f /root/pod-with-override.yaml
  Then validate with
  $ kubectl get po pod-with-override -o yaml | grep -A2 " securityContext:"
  ```
  </details>




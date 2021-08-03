# Whitelist Allowed Registries Image Policy Webhook

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/whitelist-allowed-registries-image-policy-webhook/)

In this section, we will take a look at `Whitelist Allowed Registries Image Policy Webhook`.


- As a user with access to the Kubernetes cluster I could deploy any application in the form of a pod in the cluster. And with no-one to stop me from doing that, that vulnerable image could risk the security of all other applications running on the cluster. If hackers gain access to that image, they may exploit vulnerabilities in the image and access other applications running on the cluster  or even crack down into the underlying operating system. So it's important to have governance in place to ensure images are pulled from approved registries only.

- When a request comes in , say to create a pod, it goes through authentication, authorization and then through admission controllers before being created. We talked about the mutating and validating admission controllers. We said that we could deploy our own admission webhook server and configure the validating admission webhook to make a call to this server.


-  Within the admission webhook server we check if the image name has internal-registry.io in it. If not we reject the request with an error message that says it's not from a supported registry. And we can perform any kind of checks and add any kind of error message there.

- Another option is to deploy an OPA service and then configure a validating webhook to connect to this service. And then we create policies to restrict trusted registries using the rego tool.


### References

- https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#imagepolicywebhook

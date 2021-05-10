# Whitelist Allowed Registries Image Policy Webhook
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704748)

In this section, we will take a look at Whitelist Allowed Registries Image Policy Webhook


- As a user with access to the Kubernetes cluster I could deploy any application in the form of a pod in the cluster. And with no-one to stop me from doing that, that vulnerable image could risk the security of all other applications running on the cluster. If hackers gain access to that image, they may exploit vulnerabilities in the image and access other applications running on the cluster  or even crack down into the underlying operating system. So its important to have governance in place to ensure images are pulled from approved registries only.

- When a request comes in , say to create a pod, it goes through authentication, authorization and then through admission controllers before being created. We talked about the mutating and validating admission controllers. We said that we could deploy our own admission webhook server and configure the validating admission webhook to make a call to this server.


-  Within the admission webhook server we check if the image name has internal-registry.ioin it. If not we reject the request with an error message that says its not from a supported registry.  And we can perform any kind of checks and add any kind of error message there.

- Another option is to deploy an OPA service and then configure validating webhook to connect to this service. And then we create policies to restrict trusted registries using the regotool.


### Reference

https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#imagepolicywebhook

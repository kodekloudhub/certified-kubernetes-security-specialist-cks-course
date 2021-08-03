# OPA in Kubernetes

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/opa-in-kubernetes/)

In this section, we will take a look at `OPA in Kubernetes`.

- Instead of building our own admission controller server and writing the logic for validations, we now have OPA running and all our policies in it. So instead we connect our mutating or validating webhooks now to OPA.


### How does OPA know about the resources in Kubernetes?

We can make that happen using the **`kubemanagement service`**. Kubemanagement is a service deployed as a sidecar container along with OPA and is used to replicate resource definitions from Kubernetes so they can be cached at OPA. This information can then be imported as we just saw and used to refer to objects in Kubernetes while developing policies. It is also used to load policies into OPA by simply creating a config map object in Kubernetes as opposed to loading the policies directly on OPA.


### References:

- https://kubernetes.io/blog/2019/08/06/opa-gatekeeper-policy-and-governance-for-kubernetes/

- https://www.openpolicyagent.org/docs/v0.12.2/kubernetes-admission-control/

- https://www.openpolicyagent.org/docs/latest/kubernetes-tutorial/

- https://www.openpolicyagent.org/docs/v0.11.0/guides-kubernetes-admission-control/

- Good watch: https://www.youtube.com/watch?v=QU9BGPf0hBw



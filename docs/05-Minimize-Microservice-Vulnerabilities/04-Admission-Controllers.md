# Admission Controllers
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704472)

In this section, we will take a look at Admission Controllers

  - When a request comes in , say to create a pod, it goes through authentication, authorization and then through admission controllers before being created.

  - Kubernetes admission controllers are plugins that govern and enforce how the cluster is used.

  ![admissioncontroller](../../images/admissioncontroller1.png)


### With Admission Controllers, you can

- Only permit images from certain registry.
- Don't permit runAs root user.
- Only permit certain capabilities.
- Pods always has labels.


### To see which admission plugins are enabled:

    kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugins


### Add an admission controller NamespaceAutoProvision

      vim /etc/kubernetes/manifests/kube-apiserver.yaml

      update --enable-admission-plugins=NodeRestriction

      with --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision


  - --admission-control was deprecated in 1.10 and replaced with --enable-admission-plugins.


### Reference

https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/

# Kubernetes Security Primitives

- Take me to the [Video Tutorial](https://kodekloud.com/topic/kubernetes-security-primitives-2/)

In this section we will take a look at `Kubernetes Security Primitives`.

  - The kube-apiserver is at the center of all operations within kubernetes. We interact with it through the kubectl utility or by accessing the API directly and through that you can perform almost any operation on the cluster. So that's the first line of defense.

  - All communication with the cluster, between the various components such as the ETCD cluster, kube controller manager, scheduler, apiserver, as well as those running on the worker nodes such as the kubelet and kube-proxy is secured using TLS encryption.



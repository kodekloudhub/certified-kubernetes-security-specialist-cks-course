#Kubernetes Security Primitives
- Take me to the [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31703371)

In this section we will take a look at Kubernetes Security Primitives

  - the kube-apiserver is at the center of all operations within kubernetes. We interact with it through the kubectlutility or by accessing the API directly and through that you can perform almost any operation on the cluster. So that’s the first line of defense.

  - All communication with the cluster, between the various components such as the ETCD cluster, kubecontroller manager, scheduler, apiserver, as well as those running on the worker nodes such as the kubeletand kubeproxyis secured using  TLS Encryption

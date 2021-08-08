# Kubelet Security

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/kubelet-security/)
  
In this section, we will take a look at `Kubelet Security`.

  - Kubelet is not installed by kubeadm.
  - A subset of the Kubelet's configuration parameters may be set via an on-disk config file, as a substitute for command-line flags and providing parameters via a config file is the recommended.
    ![kubelet](../../images/kubeletConfig.png)

  - Kubelet expose HTTPS endpoints which grant powerful control over the node and containers. By default, Kubelet allow unauthenticated access to this API.

  - Production clusters should enable Kubelet authentication and authorization.


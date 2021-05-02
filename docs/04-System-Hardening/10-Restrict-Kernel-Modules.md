# Restrict Kernel Modules
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704437)

In this section, we will take a look at Restrict Kernel Modules

  - The Linux Kernel has a modular design that allows the Kernel to extend its capabilities by the means of dynamically loaded kernel modules.

  - The sctp kernel module is not commonly used in Kubernetes cluster and can be black listed on the nodes by adding the following entry to the blacklist configuration file.

      ![restrictKernel](../../images/restrictKernel.png)

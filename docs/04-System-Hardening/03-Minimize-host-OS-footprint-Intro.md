# Minimize host OS footprint Intro

  - Take me to the [Video Tutuorial](https://kodekloud.com/topic/minimize-host-os-footprint-intro/)

In this section, we will take a look at `Minimize host OS footprint Intro`.

### Reducing the attack surface on host

  - One way to limit the threat to reduce the attack surface is to keep all systems in the cluster in a consistent secure state.

Some of the ways to decrease the complexity of the nodes are
  - Use the principle of least privilege so that systems, including Nodes, the software running on them, Kubernetes components and workloads only have the bare minimum access.

  - Make sure only the “Required” software is installed on the host.
  -  Limit the users and accounts that can access the host.

  - Ensure unwanted services are not exposed.

  - Ensure Kernel Modules cannot be loaded post boot.

  - Identify and fix any open port on the system.

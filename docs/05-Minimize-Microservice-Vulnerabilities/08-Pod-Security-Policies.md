# Pod Security Policies

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/pod-security-policies/)

In this section, we will take a look at `Pod Security Policies`.

 - Pod security policies help in defining policies to restrict pods from being created with specific capabilities or privileges.

 - PodSecurityPolicy plugin is not enabled by default.

 - When enabled the pod security policy admission controller observes all pod creation requests and validates the configuration against a set of pre-configured rules. If it detects a match that we have configured the request is rejected.


### Create a POD Security policy


    apiVersion: policy/v1beta1
    kind: PodSecurityPolicy
    metadata:
      name: example
    spec:
      privileged: false
      seLinux:
        rule: RunAsAny
      supplementalGroups:
        rule: RunAsAny
      runAsUser:
        rule: RunAsAny
      fsGroup:
        rule: RunAsAny

### References

- https://kubernetes.io/docs/concepts/policy/pod-security-policy/

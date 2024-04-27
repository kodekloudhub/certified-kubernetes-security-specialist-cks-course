# Lab - OPA in Kubernetes
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-opa-in-kubernetes/)

Solutions for Lab - OPA in Kubernetes:

1.  <details>
    <summary>Which is not a function of <code>Kube-mgmt</code>?</summary>

    <details>
    <summary>Reveal</summary>

    > `Manage kubernetes objects via OPA`  is not a function of Kube-mgmt.

    </details>
    </details>

1.  <details>
    <summary>What needs to be done to enable <code>kube-mgmt</code> to automatically identify policies defined in kubernetes and load them into OPA?</summary>

    * Create configmaps on Kubernetes with the label openpolicyagent.org/policy set to rego
    * Create configmaps on Kubernetes with label set to OPA
    * Create secrets on Kubernetes with name start `opa-`
    * Create configmaps on Kubernetes with name start `opa-`

    <details>
    <summary>Reveal</summary>

    > `Create configmaps on Kubernetes with the label openpolicyagent.org/policy set to rego` needs to be done to enable kube-mgmt to automatically identify policies defined in kubernetes and load them into OPA.

    </details>
    </details>

1.  <details>
    <summary>We have placed rego policies under <code>/root/untrusted-registry.rego</code> and <code>/root/unique-host.rego</code>. View the contents of these files and identify which kubernetes resources will be validated by these rego policies?</summary>

    Check `input.request.kind.kind` in rego policies file

    * untrusted-registry.rego : pod ;     unique-host.rego : pod
    * untrusted-registry.rego : ingress ; unique-host.rego : ingress
    * untrusted-registry.rego : ingress ; unique-host.rego : ingress
    * untrusted-registry.rego : ingress ; unique-host.rego : pod

    <details>
    <summary>Reveal</summary>

    > untrusted-registry.rego : pod ; unique-host.rego : ingress

    </details>
    </details>

1.  <details>
    <summary>If we were to implement the policy under <code>/root/untrusted-registry.rego</code> and create a pod as defined in <code>/root/test.yaml</code>, which of the 2 containers will error out?</summary>

    `untrusted-registry.rego` policy denies pods with image name that does not start with `hooli.com/`

    * both
    * mysql-backend
    * nginx-frontend
    * none


    <details>
    <summary>Reveal</summary>

    > nginx-frontend

    </details>
    </details>


1.  <details>
    <summary>Create a configmap for OPA using the <code>untrusted-registry.rego policy</code></summary>

    Use below files:

    * configmap file: `/root/untrusted-registry.rego`
    * configmap name : `untrusted-registry`

    Remember from CKA how to create a configmap from a file imperatively.

    <details>
    <summary>Reveal</summary>

    ```
    kubectl create configmap untrusted-registry --from-file=untrusted-registry.rego
    ```

    </details>
    </details>

1.  <details>
    <summary>Create a pod defined under <code>/root/test.yaml</code> in the namespace <code>dev</code>. Fix the OPA validation issue while creating the pod.</summary>

    NOTE: The pod is expected to be in a created state but not up and running.

    Recall that the untrusted registry policy denies creation of pods not from a certain registry named in the policy.

    Try applying the manifest as-is and observe the error

      ```
      kubectl apply -n dev -f /root/test.yaml
      ```


    <details>
    <summary>Reveal</summary>

    1. Edit `test.yaml` and ensure all container images start with `hooli.com/`
    2. Apply the edited manifest

        ```
        kubectl apply -n dev -f /root/test.yaml
        ```

    </details>
    </details>

1.  <details>
    <summary>As per policy in <code>/root/unique-host.rego</code>, which ingress resources will be denied for creation?</summary>

    * multiple ingress resources with same namespace
    * multiple ingress resources with same service
    * multiple ingress resources with same host
    * multiple ingress resources with same image name

    Check the Ingress object being compared in the `/root/unique-host.rego` policy

    <details>
    <summary>Reveal</summary>

    The following two lines in the policy give away the answer

    * `host := input.request.object.spec.rules[_].host`
    * `msg := sprintf("invalid ingress host %q (conflicts with %v/%v)", [host, other_ns, other_ingress])`

    The4 object being compared is the ingress host, and the policy is to prevent you from being able to create two ingresses referring to the same host - which is a good thing as it would confuse the ingress controller.

    > multiple ingress resources with same host

    </details>
    </details>

1.  <details>
    <summary>Create a configmap named unique-host using the rego file <code>/root/unique-host.rego</code> for OPA.</summary>

    Do the same as for Q5, but for the other rego file.

    <details>
    <summary>Reveal</summary>

    ```
    kubectl create configmap unique-host --from-file=/root/unique-host.rego
    ```
    </details>
    </details>

1.  <details>
    <summary> Create 2 ingress resources using below files. Check if you can create both resources</summary>

    * `/root/ingress-test-1.yaml`
    * `/root/ingress-test-2.yaml`


    <details>
    <summary>Reveal</summary>

    ```
    kubectl apply -f /root/ingress-test-1.yaml
    kubectl apply -f /root/ingress-test-2.yaml
    ```

    `ingress-test-2` fails with an error. If you examine both YAML files, you will see that they both refer to the same host `initech.com` which is in violation of the policy.

    Note also that if you created #2 first, then it would create and #1 would fail with that error.

    </details>
    </details>

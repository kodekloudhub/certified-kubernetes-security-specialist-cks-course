# Labs - Pod Security Admission

* Take me to the [lab](https://kodekloud.com/topic/lab-pod-security-admission/).

1. Information only.

    Run the given command

1.  <details>
    <summary>There are different levels of Pod security standards. Identify the option that does not represent a valid Pod security standard from the following list.</summary>

    * `Enforce`
    * `Restricted`
    * `Baseline`
    * `Privileged`

    <details>
    <summary>Expand</summary>

    The option that is not valid is `Enforce`

    </details>
    </details>

1.  <details>
    <summary>We want to apply pod security on namespace <code>alpha</code>. To achieve that, add the following label to the namespace <code>alpha</code></summary>

    ```
    pod-security.kubernetes.io/warn=baseline
    ```

    <details>
    <summary>Expand</summary>
    </details>

    ```
    kubectl label ns alpha pod-security.kubernetes.io/warn=baseline
    ```

    </details>

1.  <details>
    <summary>We have provided a manifest <code>baseline-pod.yaml</code> at the <code>/root</code> location of the lab terminal.</br>Inspect it and create the pod using the manifest in the <code>alpha</code> namespace.</summary>

    Note: You might see some warnings while applying manifest. Ignore them for now; we will cover them in next questions.

    <details>
    <summary>Expand</summary>

    ```
    kubectl apply -n alpha -f /root/baseline-pod.yaml
    ```

    </details>
    </details>

1.  Information only

1.  <details>
    <summary>How can a cluster administrator specify the configuration file path for the admission configuration resource in the API server?</summary>

    <details>
    <summary>Expand</summary>

    In a Kubernetes cluster, the cluster administrator can specify the configuration file path for the admission configuration resource in the API server using the --admission-control-config-file flag.

    This flag allows administrators to provide a file path that contains the configuration for admission controllers.

    Bear in mind that this file is in the API server pod's file system, therefore you must mount it as a hostPath or configmap volume.
    </details>
    </details>

1.  <details>
    <summary>We can also use multiple pod security standards together for a single namespace.</br>For this step, label the namespace <code>beta</code> with the <code>Baseline: enforce</code> and <code>Restricted: warn</code>.</summary>

    <details>
    <summary>Expand</summary>

    ```
    kubectl label ns beta \
        pod-security.kubernetes.io/enforce=baseline \
        pod-security.kubernetes.io/warn=restricted
    ```
    </details>
    </details>

1.  <details>
    <summary>We have provided a manifest <code>multi-psa.yaml</code> at the <code>/root</code> location of the lab terminal.</br>Inspect it and create the pod using the manifest in the <code>beta</code> namespace.</summary>

    Note: You might see some warnings while applying manifest. It is expected.

    <details>
    <summary>Expand</summary>

    ```
    kubectl apply -n beta -f /root/multi-psa.yaml
    ```

    </details>
    </details>

1.  Information only

1.  <details>
    <summary>We have provided an AdmissionConfiguration resource manifest at the <code>/root</code> location with name <code>admission-configuration.yaml</code>.</br>Inspect the manifest file and select the correct statement on enforced policies and the restricted levels in the provided AdmissionConfiguration resource.</summary>

    * `Baseline: Warning ans Auditing, Restricted: Enforced and Auditing`
    * `Baseline: Enforced, Restricted: Enforced`
    * `Baseline: Auditing, Restricted: Enforced`
    * `Baseline: Enforced, Restricted: Autiting and Warnning`

    <details>
    <summary>Expand</summary>

    ```bash
    cat /root/admission-configuration.yaml
    ```

    Yields the following. Comments added to this to indicate what you should be looking at

    ```yaml
    kind: AdmissionConfiguration
    plugins:
    - name: PodSecurity
        configuration:
        apiVersion: pod-security.admission.config.k8s.io/v1
        kind: PodSecurityConfiguration
        defaults:
            enforce: baseline           # <----
            enforce-version: latest
            audit: restricted           # <----
            audit-version: latest
            warn: restricted            # <----
            warn-version: latest
        exemptions:
            usernames: []
            runtimeClassNames: []
            namespaces: [my-namespace]
    ```

    Which gives the answer `Baseline: Enforced, Restricted: Autiting and Warnning`

    </details>
    </details>

1.  <details>
    <summary>We have provided an AdmissionConfiguration resource manifest at the <code>/root</code> location with name <code>admission-configuration.yaml</code>.</br>Which namespace is exempt from the policy enforced by the provided AdmissionConfiguration resource?</summary>

    * `default`
    * `my-namespace`
    * `kube-system`
    * `development`

    <details>
    <summary>Expand</summary>

    This is the same file as the previous question. Now look here...

    ```yaml
    kind: AdmissionConfiguration
    plugins:
    - name: PodSecurity
        configuration:
        apiVersion: pod-security.admission.config.k8s.io/v1
        kind: PodSecurityConfiguration
        defaults:
            enforce: baseline
            enforce-version: latest
            audit: restricted
            audit-version: latest
            warn: restricted
            warn-version: latest
        exemptions:                     # <----
            usernames: []
            runtimeClassNames: []
            namespaces: [my-namespace]  # <----
    ```

    ...giving the answer `my-namespace`

    </details>
    </details>



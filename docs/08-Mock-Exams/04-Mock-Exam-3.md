# Mock Exam 3

  - Take me to the [Mock Exam 3](https://kodekloud.com/topic/mock-exam-3-4/)

Solutions for lab - Mock Exam 3:

With questions where you need to modify API server, you can use [this resource](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md) to diagnose a failure of the API server to restart.

1.  <details>
    <summary>A kube-bench report is available at the <code>Kube-bench assessment report</code> tab. Fix the tests with <code>FAIL</code> status for <code>4 Worker Node Security Configuration</code>.</summary>

    Make changes to the `/var/lib/kubelet/config.yaml`

    After you have fixed the issues, you can update the published report in the `Kube-bench assessment report` tab by running `/root/publish_kubebench.sh` to validate results.

    <details>
    <summary>Reveal</summary>

    Update `/var/lib/kubelet/config.yaml` as below

    1. Change authorization to Webhook for authorization-mode failure:

        ```yaml
        authorization:
          mode: Webhook
        ```

    1. Add below for KernelDefaults Failure:

        ```yaml
        protectKernelDefaults: true
        ```
    </details>
    </details>


1.  <details>
    <summary>Enable auditing in this kubernetes cluster. Create a new policy file that will only log events based on the below specifications:</summary>

    * Namespace: `prod`
    * Level: `metadata`
    * Operations: `delete`
    * Resources: `secrets`
    * Log Path: `/var/log/prod-secrets.log`
    * Audit file location: `/etc/kubernetes/prod-audit.yaml`
    * Maximum days to keep the logs: `30`

    Once the policy is created it, enable and make sure that it works.

    <details>
    <summary>Reveal</summary>

    1. Create `/etc/kubernetes/prod-audit.yaml` as below:

        ```yaml
        apiVersion: audit.k8s.io/v1
        kind: Policy
        rules:
        - level: Metadata
          namespace: ["prod"]
          verb: ["delete"]
          resources:
          - group: ""
            resource: ["secrets"]
        ```

    1. Next, make sure to enable logging in api-server by adding the following arguments in the API server manifest:

        ```yaml
            - --audit-policy-file=/etc/kubernetes/prod-audit.yaml
            - --audit-log-path=/var/log/prod-secrets.log
            - --audit-log-maxage=30
        ```

    1. Then, add to the volumes and volume mounts sections as shown in the below snippets.

        ```yaml
          - name: audit
            hostPath:
              path: /etc/kubernetes/prod-audit.yaml
              type: File
          - name: audit-log
            hostPath:
              path: /var/log/prod-secrets.log
              type: FileOrCreate
        ```

        In the above, the `type` of each `hostPath` are set as follows:
        * `File` - `/etc/kubernetes/prod-audit.yaml` is a file that must pre-exist (you created it in step `i` of this solution)
        * `FileOrCreate` - The file `prod-secrets.log` will be created in directory `/var/log` if it does not exist. If it does exist, it will be appended to.<br><br>

        ```yaml
            - mountPath: /etc/kubernetes/prod-audit.yaml
              name: audit
              readOnly: true
            - mountPath: /var/log/prod-secrets.log
              name: audit-log
              readOnly: false
        ```

    1. Finally save the file and make sure that kube-apiserver restarts. This can take up to a minute. You can run the following to see the pods restarting (press `CTRL-C` to exit the watch):

        ```bash
        watch crictl ps
        ```

        If it does not start, then know how to [diagnose why](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md). Being able to do this can be the difference between passing and failing the CKS exam!

    </details>
    </details>

1.  <details>
    <summary>We have a pod definition template <code>/root/kubesec-pod.yaml</code> on <code>controlplane</code> host. Scan this template using the <code>kubesec</code> tool and you will notice some failures.</summary>

    Fix the failures in this file and save the success report in `/root/kubesec_success_report.json`.

    Make sure that the final kubesec scan status is passed.

    <details>
    <summary>Reveal</summary>

    1. Scan the pod YAML

        ```
        kubesec scan /root/kubesec-pod.yaml
        ```

        You will see failure message as:

        > `containers[] .securityContext .privileged == true`

    1. Update privileged flag in `/root/kubesec-pod.yaml`

        ```yaml
        privileged: false
        ```

    1. Then run

        ```
        kubesec scan /root/kubesec-pod.yaml
        kubesec scan /root/kubesec-pod.yaml > /root/kubesec_success_report.json
        ```

    </details>


1.  <details>
    <summary>In the dev namespace create below resources...</summary>

    - A role `dev-write` with access to get, watch, list and create pods in the same namespace.
    - A Service account called `developer` and then bind `dev-write` role to it with a rolebinding called `dev-write-binding`.
    - Finally, create a pod using the template `/root/dev-pod.yaml`. The pod should run with the service account developer. Update `/root/dev-pod.yaml` as necessary

    <details>
    <summary>Reveal </summary>

    1. Create role dev-write as below:

        ```bash
        kubectl create role -n dev dev-write --resource pods --verb get,watch,list,create
        ```

    1. Create service account developer and rolebinding as below:

        ```bash
        kubectl create sa -n dev developer
        kubectl create rolebinding -n dev dev-write-binding --role dev-write --serviceaccount dev:developer
        ```

    1. Update `/root/dev-pod.yaml`. Adjust `serviceAccount` and `namespace` accordingly and deploy pod.

    </details>
    </details>

1.  <details>
    <summary>Try to create a pod using the template defined in <code>/root/beta-pod.yaml</code> in the namespace <code>beta</code>. This should result in a failure.</summary>

    Troubleshoot and fix the OPA validation issue while creating the pod. You can update /root/beta-pod.yaml as necessary.

    The Rego configuration map for OPA is in `untrusted-registry` under `opa` namespace.

    NOTE: In the end pod need not to be successfully running but make sure that it passed the OPA validation and gets created.

    <details>
    <summary>Reveal</summary>

    If you inspect the rego file defined in the configmap called `untrusted-registry`, you will see that it denies repositories that do not begin with `kodekloud.io/`.

    To fix this, update the image URL to `kodekloud.io/` and then create the pod:

    1.  Inspect the configmap

        ```
        kubectl get cm -n opa untrusted-registry -o yaml
        ```

    1. Edit `/root/beta-pod.yaml` and adjust the image...

        ```yaml
          - image: kodekloud.io/google-samples/node-hello:1.0
        ```

    1.  Create it

        ```bash
        kubectl create -f /root/beta-pod.yaml
        ```

    NOTE: The pod will now be created as it passes the policy checks. However, it will not run as such an image does not exist.

    </details>
    </details>


1.  <details>
    <summary>We want to deploy an <code>ImagePolicyWebhook</code> admission controller to secure the deployments in our cluster.</summary>

    - Fix the error in `/etc/kubernetes/pki/admission_configuration.yaml` which will be used by `ImagePolicyWebhook`
    - Make sure that the policy is set to implicit deny. If the webhook service is not reachable, the configuration file should automatically reject all the images.
    - Enable the plugin on API server.

    The kubeconfig file for already created imagepolicywebhook resources is under `/etc/kubernetes/pki/admission_kube_config.yaml`

    <details>
    <summary>Reveal</summary>

    1. Update `/etc/kubernetes/pki/admission_configuration.yaml` and make required changes:

        * Set the path to the kubeconfig file. Note that you are not told where this file is, so we need to look for it.
            * Check the directory `/etc/kubernetes/pki` where the admission configuration is. You should find it in the same directory as `admission_kube_config.yaml`.
            * Note that when API server loads the admission configuration, the path for `kubeConfigFile` refers to the file system of the *container* not the host. Therefore you should validate API server's volumes and volume mounts to know where in the container the host path `/etc/kubernetes/pki` will be mounted. Luckily it is the same path.
        * Set the path to the `kubeConfigFile` and change `defaultAllow` to `false` to satisfy the "automatically reject" condition.

            ```yaml
            apiVersion: apiserver.config.k8s.io/v1
            kind: AdmissionConfiguration
            plugins:
            - name: ImagePolicyWebhook
              configuration:
                imagePolicy:
                  kubeConfigFile: /etc/kubernetes/pki/admission_kube_config.yaml
                  allowTTL: 50
                  denyTTL: 50
                  retryBackoff: 500
                  defaultAllow: false
            ```

    1. Update /etc/kubernetes/manifests/kube-apiserver.yaml as below:

        ```yaml
            - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
            - --admission-control-config-file=/etc/kubernetes/pki/admission_configuration.yaml
        ```

    1. Finally save the file and make sure that kube-apiserver restarts. This can take up to a minute. You can run the following to see the pods restarting (press `CTRL-C` to exit the watch):

        ```bash
        watch crictl ps
        ```

        If it does not start, then know how to [diagnose why](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md). Being able to do this can be the difference between passing and failing the CKS exam!

    </details>
    </details>

1.  <details>
    <summary>Delete pods from <code>alpha</code> namespace which are not immutable.</summary>

    Examine the pods by getting them with `-o yaml`. Look for settings that voilate the condition of immutability.

    <details>
    <summary>Reveal</summary>

    * Pod `solaris` is immutable as it has `readOnlyRootFilesystem: true` so it should *not* be deleted.
    * Pod `sonata` is running with `readOnlyRootFilesystem: false` thus it can be mutated so should be deleted.
    * Pod `triton` has no setting for `readOnlyRootFilesystem`. The default for this when not present is `false` therefore it should be deleted.

    </details>
    </details>



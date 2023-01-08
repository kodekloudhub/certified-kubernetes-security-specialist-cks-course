# Mock Exam 3

  - Take me to the [Mock Exam 3](https://kodekloud.com/topic/mock-exam-3-4/)

Solutions for lab - Mock Exam 3:

With questions where you need to modify API server, you can use [this resource](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md) to diagnose a failure of the API server to restart.

- 1
  <details>

    Update `/var/lib/kubelet/config.yaml` as below

    Change authorization to Webhook for authorization-mode failure:

    ```yaml
    authorization:
      mode: Webhook
    ```

    Add below for KernelDefaults Failure:

    ```yaml
    protectKernelDefaults: true
    ```
  </details>


- 2
  <details>

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

    1. Next, make sure to enable logging in api-server:

        ```
        - --audit-policy-file=/etc/kubernetes/prod-audit.yaml
        - --audit-log-path=/var/log/prod-secrets.log
        - --audit-log-maxage=30
        ```

    1. Then, add volumes and volume mounts as shown in the below snippets.

        ```yaml
        volumes:
          - name: audit
            hostPath:
              path: /etc/kubernetes/prod-audit.yaml
              type: File
          - name: audit-log
            hostPath:
              path: /var/log/prod-secrets.log
              type: FileOrCreate
        ```

        ```yaml
        volumeMounts:
          - mountPath: /etc/kubernetes/prod-audit.yaml
            name: audit
            readOnly: true
          - mountPath: /var/log/prod-secrets.log
            name: audit-log
            readOnly: false
        ```

    1. Finally save the file and make sure that kube-apiserver restarts

  </details>

- 3
  <details>

  1. Scan the pod YAML

      ```
      $ kubesec scan /root/kubesec-pod.yaml
      ```
     You will see failure message as:

     > `containers[] .securityContext .privileged == true`

  1. Update privileged flag in `/root/kubesec-pod.yaml`

      ```yaml
      privileged: false
      ```

  1. Then run

      ```
      $ kubesec scan /root/kubesec-pod.yaml
      $ kubesec scan /root/kubesec-pod.yaml > /root/kubesec_success_report.json
      ```

  </details>


- 4

  <details>

  1. Create role dev-write as below:

      ```bash
      cat <<EOF | kubectl apply -f -
      apiVersion: rbac.authorization.k8s.io/v1
      kind: Role
      metadata:
        namespace: dev
        name: dev-write
      rules:
      - apiGroups: [""] # "" indicates the core API group
        resources: ["pods"]
        verbs: ["get", "watch", "list", "create"]
      EOF
      ```

  1. Create service account developer and rolebinding as below:

      ```bash
      $ kubectl create sa developer -n dev
      $ cat <<EOF | kubectl apply -f -
      apiVersion: rbac.authorization.k8s.io/v1
      kind: RoleBinding
      metadata:
        name: dev-write-binding
        namespace: dev
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: Role
        name: dev-write
      subjects:
      - kind: ServiceAccount
        name: developer
        namespace: dev
      EOF
      ```

  1. Update serviceaccount in `/root/dev-pod.yaml` to `developer` and deploy pod

  </details>


- 5

  <details>

  If you inspect the rego file defined in the configmap called untrusted-registry, you will see that it denies repositories that do not begin with `kodekloud.io/`.

  To fix this, update the image URL to `kodekloud.io/` and then create the pod:

    ```yaml
    - image: kodekloud.io/google-samples/node-hello:1.0
    ```

  NOTE: The pod will now be created as it passes the policy checks. However, it will not run as such an image does not exist.

  </details>


- 6

  <details>


  1. Update /etc/kubernetes/pki/admission_configuration.yaml and add the path to the kubeconfig file:

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

      ```
      - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
      - --admission-control-config-file=/etc/kubernetes/pki/admission_configuration.yaml
      ```

  1. API server will automatically restart and pickup this configuration

  </details>


- 7
  <details>


  * Pod solaris is immutable as it have readOnlyRootFilesystem: true so it should not be deleted.
  * Pod sonata is running with privileged: true and triton doesn't define

  `readOnlyRootFilesystem: true` so both break the concept of immutability and should be deleted.

  </details>



# Mock Exam 3
  - Take me to [Mock Exam 3](https://kodekloud.com/courses/1378608/lectures/31704782)

Solutions Mock Exam 3

- A kube-bench report is available at the `Kube-bench assessment report tab. Fix the tests with FAIL status for 4 Worker Node Security Configuration .
  Make changes to the /var/lib/kubelet/config.yaml
  After you have fixed the issues, you can update the published report in the Kube-bench assessment report tab by running /root/publish_kubebench.sh to validate results.
  <details>

  ```
  # Update /var/lib/kubelet/config.yaml as below
  #Change authorization to Webhook for authorization-mode failure:

  authorization:
    mode: Webhook

  # Add below for KernelDefaults Failure:

    protectKernelDefaults: true
  ```
  </details>

- Enable auditing in this kubernetes cluster. Create a new policy file that will only log events based on the below specifications:
  Namespace: prod
  Level: metadata
  Operations: delete
  Resources: secrets
  Log Path: /var/log/prod-secrets.log
  Audit file location: /etc/kubernetes/prod-audit.yaml
  Maximum days to keep the logs: 30
  Once the policy is created it, enable and make sure that it works.
  <details>

  ```
  # Create /etc/kubernetes/prod-audit.yaml as below:

  apiVersion: audit.k8s.io/v1
  kind: Policy
  rules:
  - level: Metadata
    namespace: ["prod"]
    verb: ["delete"]
    resources:
    - group: ""
      resource: ["secrets"]

  # Next, make sure to enable logging in api-server:

  - --audit-policy-file=/etc/kubernetes/prod-audit.yaml
  - --audit-log-path=/var/log/prod-secrets.log
  - --audit-log-maxage=30

  # Then, add volumes and volume mounts as shown in the below snippets.

  volumes:
    - name: audit
      hostPath:
        path: /etc/kubernetes/prod-audit.yaml
        type: File
    - name: audit-log
      hostPath:
        path: /var/log/prod-secrets.log
        type: FileOrCreate
  volumeMounts:
    - mountPath: /etc/kubernetes/prod-audit.yaml
      name: audit
      readOnly: true
    - mountPath: /var/log/prod-secrets.log
      name: audit-log
      readOnly: false

  # The save the file and make sure that kube-apiserver restarts
  ```
  </details>

- Enable PodSecurityPolicy in the Kubernetes API server.
  Create a PSP with below conditions:
    PSP name : pod-psp
    Privilege to run as root on host: false
    Allowed volumes to mount on pod: configMap,secret,emptyDir,hostPath
    seLinux, runAsUser, supplementalGroups, fsGroup: RunAsAny
  Fix the pod definition /root/pod.yaml based on this PSP and deploy the pod. Ensure that the pod is running after applying the above pod security policy.
  <details>

  ```
  # Add PodSecurityPolicy admission controller to --enable-admission-plugins list to /etc/kubernetes/manifests/kube-apiserver.yaml It should look like below:

      - --enable-admission-plugins=NodeRestriction,PodSecurityPolicy

  # API server will pickup this config automatically without need of restart
  Create PSP as below:

  $ cat <<EOF > /root/psp.yaml

  apiVersion: policy/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: pod-psp
  spec:
    privileged: false
    seLinux:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    volumes:
    - configMap
    - secret
    - emptyDir
    - hostPath
  EOF

  $ kubectl apply -f /root/psp.yaml

  # Update pod definition by making privileged: # False and removing capabilities

  $ cat <<EOF > /root/pod.yaml

  apiVersion: v1
  kind: Pod
  metadata:
      name: psp-app
  spec:
      containers:
          -
              name: example-app
              image: ubuntu
              command: ["sleep" , "3600"]
              securityContext:
                privileged: False
                runAsUser: 0
      volumes:
      -   name: data-volume
          hostPath:
            path: '/data'
            type: Directory
  EOF

  $ kubectl apply -f /root/pod.yaml
  ```
  </details>

- We have a pod definition template /root/kubesec-pod.yaml on controlplane host. Scan this template using the kubesec tool and you will notice some failures.
  Fix the failures in this file and save the success report in /root/kubesec_success_report.json.
  Make sure that the final kubesec scan status is passed.
  <details>

  ```
  # Run
  $ kubesec scan /root/kubesec-pod.yaml
  # You will see failure message as:
  containers[] .securityContext .privileged == true

  # Update privileged flag in /root/kubesec-pod.yaml

  privileged: false

  # Then run

  $ kubesec scan /root/kubesec-pod.yaml
  $ kubesec scan /root/kubesec-pod.yaml > /root/kubesec_success_report.json
  ```
  </details>

- In the dev namespace create below resources:
  A role dev-write with access to get, watch, list and create pods in the same namespace.
  A Service account called developer and then bind dev-write role to it with a rolebinding called dev-write-binding.
  Finally, create a pod using the template /root/dev-pod.yaml. The pod should run with the service account developer. Update /root/dev-pod.yaml as necessary
  <details>

  ```
  # Create role dev-write as below:

  $ cat <<EOF | kubectl apply -f -

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

  # Create service account developer and rolebinding as below:

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

  # Update serviceaccount in /root/dev-pod.yaml to developer and deploy pod
  ```
  </details>

- Try to create a pod using the template defined in /root/beta-pod.yaml in the namespace beta. This should result in a failure.
  Troubleshoot and fix the OPA validation issue while creating the pod. You can update /root/beta-pod.yaml as necessary.
  The Rego configuration map for OPA is in untrusted-registry under opa namespace.
  NOTE: In the end pod need not to be successfully running but make sure that it passed the OPA validation and gets created.
  <details>

  ```
  # If you inspect the rego file defined in the configmap called untrusted-registry, you will see that it denies repositories that do not begin with kodekloud.io/.

  # To fix this, update the image URL to kodekloud.io/ and then create the pod:

    - image: kodekloud.io/google-samples/node-hello:1.0

  # NOTE: The pod will now be created as it passes the policy checks. However, it will not run as such an image does not exist.
  ```
  </details>

- We want to deploy an ImagePolicyWebhook admission controller to secure the deployments in our cluster.
  Fix the error in /etc/kubernetes/pki/admission_configuration.yaml which will be used by ImagePolicyWebhook
  Make sure that the policy is set to implicit deny. If the webhook service is not reachable, the configuration file should automatically reject all the images.
  Enable the plugin on API server.
  The kubeconfig file for already created imagepolicywebhook resources is under /etc/kubernetes/pki/admission_kube_config.yaml
  <details>

  ```
  # Update /etc/kubernetes/pki/admission_configuration.yaml and add the path to the kubeconfig file:

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

  # Update /etc/kubernetes/manifests/kube-apiserver.yaml as below:

      - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
      - --admission-control-config-file=/etc/kubernetes/pki/admission_configuration.yaml

  # API server will automatically restart and pickup this configuration
  ```
  </details>

- Delete pods from alpha namespace which are not immutable.
  Note: Any pod which uses elevated privileges and can store state inside the container is considered to be non-immutable.
  <details>

  ```
  # Pod solaris is immutable as it have readOnlyRootFilesystem: true so it should not be deleted.

  # Pod sonata is running with privileged: true and triton doesn't define

  readOnlyRootFilesystem: true so both break the concept of immutability and should be deleted.
  ```
  </details>

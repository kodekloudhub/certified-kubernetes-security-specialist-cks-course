# Mock Exam 2

  - Take me to the [Mock Exam 2](https://kodekloud.com/topic/mock-exam-2-6/)

Solutions for lab - Mock Exam 2:

- 1
  <details>

  ```
  # Use this YAML file below:

  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-redis-access
    namespace: prod-x12cs
  spec:
    podSelector:
      matchLabels:
        run: redis-backend
    policyTypes:
    - Ingress
    ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            access: redis
      - podSelector:
          matchLabels:
            backend: prod-x12cs
      ports:
      - protocol: TCP
        port: 6379
  ```
  </details>


- 2
  <details>

  ```
  # Use this YAML file below:

  kind: NetworkPolicy
  apiVersion: networking.k8s.io/v1
  metadata:
    name: allow-app1-app2
    namespace: apps-xyz
  spec:
    podSelector:
      matchLabels:
        tier: backend
        role: db
    ingress:
    - from:
      - podSelector:
          matchLabels:
            name: app1
            tier: frontend
      - podSelector:
          matchLabels:
            name: app2
            tier: frontend
  ```
  </details>


- 3

  <details>

  ```
  # Update the Pod to use the field automountServiceAccountToken: false
  # Using this option makes sure that the service account token secret is not mounted in the pod at the location '/var/run/secrets/kubernetes.io/serviceaccount'

  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      run: apps-cluster-dash
    name: apps-cluster-dash
    namespace: gamma
  spec:
    containers:
    - image: nginx
      name: apps-cluster-dash
    serviceAccountName: cluster-view
    automountServiceAccountToken: false
  ```
  </details>


- 4

  <details>

  ```
  # Add the below rule to /etc/falco/falco_rules.local.yaml node01 and restart falco using "systemctl restart falco.service" to override the currentrule

  - rule: Terminal shell in container
    desc: A shell was used as the entrypoint/exec point into a container with an attached terminal.
    condition: >
      spawned_process and container
      and shell_procs and proc.tty != 0
      and container_entrypoint
      and not user_expected_terminal_shell_in_container_conditions
    output: >
      %evt.time.s,%user.uid,%container.id,%container.image.repository
    priority: ALERT
    tags: [container, shell, mitre_execution]
  ```
  </details>


- 5

  <details>

  ```
  # The role called dev-user-access has been created for all three namespaces: dev-a. dev-b and dev-z. However, the role in the 'dev-z' namespace grants martin access to all operation on all pods. To fix this, delete and re-create the role as below:

  apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
      name: dev-user-access
      namespace: dev-z
  rules:
      -
          apiGroups:
              - ""
          resources:
              - pods
          verbs:
              - get
              - list
  ```
  </details>


- 6

  <details>

  ```
  # First SSH to node01 from controlplane:

  controlplane $ ssh node01

  # Next, check the process which is bound to port 8088 on this node using netstat"

  node01 $ netstat -natulp | grep 8088

  # This shows that the the process openlitespeed is the one which is using this port.
  # Check if any service is running with the same name

  node01 $ systemctl list-units  -t service --state active | grep -i openlitespeed
  lshttpd.service                    
  loaded active running OpenLiteSpeed HTTP Server

  # This shows that a service called openlitespeed is managed by lshttpd.service which is currently active.

  # Stop the service and disable it

  node01 $ systemctl stop lshttpd
  node01 $ systemctl disable lshttpd

  # Finally, check for the package by the same name

  node01 $ apt list --installed | grep openlitespeed

  # Uninstall the package

  node01 $ apt remove openlitespeed -y
  ```
  </details>


- 7

  <details>

  ```
  # The path to the seccomp profile is incorrectly specified for the omega-app pod.
  # As per the question, the profile is created at /var/lib/kubelet/seccomp/custom-profiles.json

  controlplane $ kubectl -n omega describe omega-app

  .
  .
  .
  Events:
    Type     Reason  Age              From             Message
    ----     ------  ----             ----             -------
    Normal   Pulled  5s (x3 over 7s)  kubelet, node01  Container image "hashicorp/http-echo:0.2.3" already present on machine
    Warning  Failed  5s (x3 over 7s)  kubelet, node01  Error: failed to generate security options for container "test-container": failed to generate seccomp security options for container: cannot load seccomp profile "/var/lib/kubelet/seccomp/profiles/custom-profile.json": open /var/lib/kubelet/seccomp/profiles/custom-profile.json: no such file or directory

  # Fix the seccomp profile path in the POD Definition file
  Fix omega-app.yaml

  securityContext:
        seccompProfile:
          localhostProfile: custom-profile.json
          type: Localhost

  # Next, on node01, update the custom-profile.json to allow 'read' and 'write' syscalls.
  Once done, you should see an output similar to below:

  node01 $ cat /var/lib/kubelet/seccomp/custom-profile.json | jq -r '.syscalls[].names[]' | grep -w write

  write

  node01 $ cat /var/lib/kubelet/seccomp/custom-profile.json | jq -r '.syscalls[].names[]' | grep -w read

  read

  # Finally, re-create the pod

  controlplane $ kubectl replace -f /root/CKS/omega-app.yaml

  # The POD should now run successfully.
  NOTE:
  # It may still run even if the above two syscalls are not added. However, adding the syscalls is required to successfully complete this question.
  ```
  </details>


- 8

  <details>

  ```
  # Remove the SYS_ADMIN capability from the container for the simple-webapp-1 pod in the POD definition file and re-run the scan.

  controlplane $ kubesec scan /root/CKS/simple-pod.yaml > /root/CKS/kubesec-report.txt

  #The fixed report should PASS with a message like this:

  [
    {
      "object": "Pod/simple-webapp-1.default",
      "valid": true,
      "fileName": "API",
      "message": "Passed with a score of 3 points",
      "score": 3,
  ```
  </details>

- 9

  <details>

  ```
  # Run trivy image scan on all of the images and check which one does not have HIGH or CRITICAL vulnerabilities.

  controlplane $ trivy image nginx:alpine

  2021-04-26T03:41:49.033Z        INFO    Detecting Alpine vulnerabilities...
  2021-04-26T03:41:49.041Z        INFO    Trivy skips scanning programming language libraries because no supported file was detected
  nginx:alpine (alpine 3.13.5)
  ============================
  Total: 0 (HIGH: 0, CRITICAL: 0)
  Next, use this image to create the pod

  controlplane $ kubectl -n seth run secure-nginx-pod --image nginx:alpine
  ```
  </details>



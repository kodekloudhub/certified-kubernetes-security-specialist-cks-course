# Mock Exam 2
  - Take me to [Mock Exam 2](https://kodekloud.com/courses/1378608/lectures/31704781)

Solutions Mock Exam 2

- A pod called redis-backend has been created in the prod-x12cs namespace. It has been exposed as a service of type ClusterIP. Using a network policy called allow-redis-access, lock down access to this pod only to the following:
  1. Any pod in the same namespace with the label backend=prod-x12cs.
  2. All pods in the prod-yx13cs namespace.
  All other incoming connections should be blocked.
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

- A few pods have been deployed in the apps-xyz namespace. There is a pod called redis-backend which serves as the backend for the apps app1 and app2. The pod called app3 on the other hand, does not need access to this redis-backend pod. Create a network policy called allow-app1-app2 that will only allow incoming traffic from app1 and app2 to the redis-pod.
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

- A pod has been created in the gamma namespace using a service account called cluster-view. This service account has been granted additional permissions as compared to the default service account and can view resources cluster-wide on this Kubernetes cluster. While these permissions are important for the application in this pod to work, the secret token is still mounted on this pod.
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

- A pod in the sahara namespace has generated alerts that a shell was opened inside the container.
 Change the format of the output so that it looks like below:
 ALERT timestamp of the event without nanoseconds,User ID,the container id,the container image repository
 Make sure to update the rule in such a way that the changes will persists across Falco updates.
 You can refer the falco documentation Here
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

- martin is a developer who needs access to work on the dev-a, dev-b and dev-z namespace. He should have the ability to carry out any operation on any pod in dev-a and dev-b namespaces. However, on the dev-z namespace, he should only have the permission to get and list the pods.
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

- On the node01 of the cluster, an unknown process is bound to port 8088. Identify the process and prevent it from running again by stopping and disabling any associated services. Finally, remove the package that was responsible for starting this process.
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

- A pod has been created in the omega namespace using the pod definition file located at /root/CKS/omega-app.yaml. However, there is something wrong with it and the pod is not in a running state.
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

- A pod definition file has been created at /root/CKS/simple-pod.yaml .
  Using the kubesec tool, generate a report for this pod definition file and fix the major issues so that the subsequent scan report no longer fails.
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

- Create a new pod called secure-nginx-pod in the seth namespace. Use one of the images from the below which has no HIGH or CRITICAL vulnerabilities.

  - nginx
  - nginx:1.19
  - nginx:1.17
  - nginx:alpine
  - gcr.io/google-containers/nginx
  - bitnami/nginx:latest


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

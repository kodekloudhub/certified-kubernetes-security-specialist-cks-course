# Mock Exam 2

  - Take me to the [Mock Exam 2](https://kodekloud.com/topic/mock-exam-2-6/)

Solutions for lab - Mock Exam 2:

With questions where you need to modify API server, you can use [this resource](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md) to diagnose a failure of the API server to restart.


1.  <details>
    <summary>A pod called <code>redis-backend</code> has been created in the <code>prod-x12cs namespace</code>. It has been exposed as a service of type <code>ClusterIP</code>. </summary>

    Using a network policy called allow-redis-access, lock down access to this pod only to the following:

    1. Any pod in the same namespace with the label `backend=prod-x12cs``.
    1. All pods in the `prod-yx13cs` namespace.

    All other incoming connections should be blocked.

    Use the *existing labels* when creating the network policy.

    ---

    This is not dissimilar to Q8 in Mock Exam 1. We are going to need an initial pod selector to say which pods the rules will apply to, and two rules - remember that each rule begins with `-` - one for each of the conditions above.

    Also we are told to use existing labels, so that's also going to include labels on namespaces, therefore run a command to get namespace with labels to see what these labels are.

    We are also told about a clusterIP service. View this service to find any port restriction we should also apply.

    <details>
    <summary>Reveal policy</summary>

    ```yaml
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
        - podSelector:          # Rule 1, for first condition
            matchLabels:
              backend: prod-x12cs
        - namespaceSelector:    # Ruie 2 for second condition
            matchLabels:
              access: redis
        ports:
        - protocol: TCP
          port: 6379
    ```

    </details>
    </details>


1.  <details>
    <summary>A few pods have been deployed in the <code>apps-xyz</code> namespace. There is a pod called <code>redis-backend</code> which serves as the backend for the apps<code>app1</code> and <code>app2</code>. The pod called <code>app3</code> on the other hand, does not need access to this <code>redis-backend</code> pod. Create a network policy called <code>allow-app1-app2</code> that will only allow incoming traffic from <code>app1</code> and <code>app2</code> to the <code>redis-pod</code>.</summary>

    Make sure that all the available labels are used correctly to target the correct pods. Do not make any other changes to these objects.

    ---

    1. View the labels on all the pods

        ```
        kubectl get pods -n apps-xyz --show-labels
        ```

    1. Note the labels on `redis-backend` pod. This will give use the initial pod selector to select the pod to apply the policy to.
    1. Note that all three `app` pods carry the label `tier=frontend`, therefore we are going to require *all* the labels for `app1` and `app2` in order to exclude `app3`, which means we will need one `podSelector` rule for each of the two pods we want to include. Remember that each rule begins with `-`


    <details>
    <summary>Reveal policy</summary>

    Final policy:

    ```yaml
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


3.  <details>
    <summary>A pod has been created in the <code>gamma</code> namespace using a service account called <code>cluster-view</code>. This service account has been granted additional permissions as compared to the default service account and can <code>view</code> resources cluster-wide on this Kubernetes cluster. While these permissions are important for the application in this pod to work, the secret token is still mounted on this pod.</summary>

    Secure the pod in such a way that the secret token is no longer mounted on this pod. You may delete and recreate the pod.

    ---

    This is quite easy. There are two changes we need to make to the pod manifest. Since there is little that may be changed in a pod, then it will indeed have to be deleted and recreated.

    <details>
    <summary>Reveal</summary>


    1.  Update the Pod to use the field

        ```yaml
        automountServiceAccountToken: false
        ```

        Using this option makes sure that the service account token secret is not mounted in the pod at the location `/var/run/secrets/kubernetes.io/serviceaccount`, *provided* you have removed any explicit volumes and volumeMounts which will be present if you extracted the manifest from the running pod with `-o yaml`

        Note that this option merely tells the controller not to add a volume and mount if not already present. It does *not* remove any existing mount for the secret, therefore...

    1. If you did retreve the pod manifest with `-o yaml` then delete any volume and mount information referring to `/var/run/secrets/kubernetes.io/serviceaccount` before recreating the pod!


    ```yaml
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
      # Note that we have manually deleted volume/mount that previously existed for secret.
    ```

    </details>
    </details>


4.  <details>
    <summary>A pod in the <code>sahara</code> namespace has generated alerts that a shell was opened inside the container.</summary>

    To recognize such alerts, set the priority to ALERT and change the format of the output so that it looks like the below:

    ALERT timestamp of the event without nanoseconds,User ID,the container id,the container image repository
    Make sure to update the rule in such a way that the changes will persists across Falco updates.

    You can refer the falco documentation [Here](https://falco.org/docs/rules/supported-fields/)

    ---

    Pretty much all Falco questions will involve you modifying an existing rule to change its output in order with what the question asks. You should not be asked to create a new rule from scratch.

    Modifying an existing rule means finding it in `/etc/falco/falco_rules.yaml`, copying it to `/etc/falco/falco_rules.local.yaml` and then making the changes there.

    The question suggests that Falco is already running and should be logging the rule we need to change, so here's how to solve

    1. Get the falco log to see the existing rule

        ```bash
        journalctl -fu falco | grep shell
        ```

        We see something like this

        ```
        Aug 06 15:18:31 controlplane falco[7283]: 15:18:31.802309590: Notice A shell was spawned in a container with an attached terminal (user=<NA> user_loginuid=-1 apps-240616 (id=3a66590fa9e3) shell=bash parent=runc cmdline=bash -c date
        ```

    1. Now we know what the current message looks like, we can look for it in the existing rules. Tune the `-A` and `-B` arguments of grep until you can see the entire rule.

        ```bash
        grep -A 10 -B 10 'A shell was spawned in a container' /etc/falco/falco_rules.yaml
        ```

        Select and copy the rule (use mouse and rght-click, copy)

    1.  Paste the rule into the local rules file

        ```bash
        vi /etc/falco/falco_rules.local.yaml
        ```

        Enter INSERT mode and paste with the mouse

    1.  Make the required edits to this rule, which in this case is the `outputs` section as we are asked to change what is logged, not the condtions for the event.

        <details>
        <summary>Reveal</summary>

        ```yaml
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

    1. Restart falco using `systemctl restart falco` to override the current rule


1.  <details>
    <summary><code>martin</code> is a developer who needs access to work on the <code>dev-a</code>, <code>dev-b</code> and <code>dev-z</code> namespace. He should have the ability to carry out any operation on any pod in <code>dev-a</code> and <code>dev-b</code> namespaces. However, on the <code>dev-z</code> namespace, he should only have the permission to <code>get</code> and <code>list</code> the pods.</summary>

    The current set-up is too permissive and violates the above condition. Use the above requirement and secure martin's access in the cluster. You may re-create objects, however, make sure to use the same name as the ones in effect currently.

    ---

    So this one is about `role`, and the fact that the permissions are wrong.


    1. Quickly find the roles by getting all roles in the cluster and grepping for anything that matches `dev`. This will match all the above mentioned namespaces whose names will be present in the output

        ```
        kubectl get role -A | grep dev
        ```

        Note that there is a role `dev-user-access` in each of the three namespaces indicated. From how the question is worded and the fact that there is only this role in each of the three namespaces, then we can deduce that it is this role that we must examine.

    1.  Examine the role permissions

        ```
        kubectl describe role -n dev-a dev-user-access
        kubectl describe role -n dev-b dev-user-access
        kubectl describe role -n dev-z dev-user-access
        ```

        Note that each of these roles have the same access - all access for `pods`. We are told that this is correct for `dev-a` and `dev-b`, but not for `dev-z` so it is that role we need to change.

    1.  Fix the role

        ```
        kubectl edit role -n dev-z dev-user-access
        ```

        Change it to allow only `get` and `list`

        <details>
        <summary>Reveal</summary>

        ```yaml
        apiVersion: rbac.authorization.k8s.io/v1
        kind: Role
        metadata:
          name: dev-user-access
          namespace: dev-z
        rules:
        - apiGroups:
          - ""
          resources:
          - pods
          verbs:
          - get
          - list
        ```
        </details>
    </details>


1.  <details>
    <summary>On the <code>controlplane</code> node, an unknown process is bound to the port <code>8088</code>. Identify the process and prevent it from running again by stopping and disabling any associated services. Finally, remove the package that was responsible for starting this process.</summary>

    1. Check the process which is bound to port 8088 on this node using netstat

        ```bash
        netstat -ptln | grep 8088
        ```

        Netstat arguments

        * `p` - Show the PID and name of the program to which each socket belongs.
        * `t` - Show TCP sockets.
        * `l` - Show only listening sockets.
        * `n` - Show numerical addresses instead of trying to determine symbolic host, port or user names.

        This shows that the the process `openlitespeed` is the one which is using this port.

    1. Check if any service is running with the same name

        ```
        systemctl list-units  -t service --state active | grep -i openlitespeed
        ```
        >  `lshttpd.service     loaded active running OpenLiteSpeed HTTP Server`


        This shows that a service called `openlitespeed` is managed by `lshttpd.service` which is currently active.

    1. Stop the service and disable it

        ```bash
        systemctl stop lshttpd
        systemctl disable lshttpd
        ```

    1.  Check for the package by the same name

        ```bash
        apt list --installed | grep openlitespeed
        ```

    1. Uninstall the package

        ```bash
        apt remove openlitespeed -y
        ```

    </details>


1.  <details>
    <summary>A pod has been created in the <code>omega</code> namespace using the pod definition file located at <code>/root/CKS/omega-app.yaml</code>. However, there is something wrong with it and the pod is not in a running state.</summary>

    We have used a custom seccomp profile located at `/var/lib/kubelet/seccomp/custom-profile.json` to ensure that this pod can only make use of limited syscalls to the Linux Kernel of the host operating system. However, it appears the profile does not allow the `read` and `write` syscalls. Fix this by adding it to the profile and use it to start the pod.

    ---

    1. Find out why the pod isn't starting

        ```
        kubectl -n omega describe pod omega-app
        ```

        Check the `Events` section:

        ```
        Events:
          Type     Reason     Age                   From               Message
          ----     ------     ----                  ----               -------
          Normal   Scheduled  23m                   default-scheduler  Successfully assigned omega/omega-app to controlplane
          Normal   Pulling    23m                   kubelet            Pulling image "hashicorp/http-echo:0.2.3"
          Normal   Pulled     23m                   kubelet            Successfully pulled image "hashicorp/http-echo:0.2.3" in 2.094171079s (9.750752135s including waiting)
          Warning  Failed     21m (x12 over 23m)    kubelet            Error: failed to create containerd container: cannot load seccomp profile "/var/lib/kubelet/seccomp/profiles/custom-profile.json": open /var/lib/kubelet/seccomp/profiles/custom-profile.json: no such file or directory
        ```

        The path to the seccomp profile is incorrectly specified for the omega-app pod.</br>
        As per the question, the profile is created at `/var/lib/kubelet/seccomp/custom-profile.json`.

    2. Fix the seccomp profile path in the POD Definition file `/root/CKS/omega-app.yaml`</br>

        ```bash
        vi /root/CKS/omega-app.yaml
        ```

        Update the security context as follows

        ```yaml
        securityContext:
          seccompProfile:
            localhostProfile: custom-profile.json
            type: Localhost
        ```

    3. Next, update the `custom-profile.json` to allow `read` and `write` syscalls.

        ```bash
        vi /var/lib/kubelet/seccomp/custom-profile.json
        ```

    4. Finally, re-create the pod

        ```
        kubectl replace -f /root/CKS/omega-app.yaml --force
        ```

        > pod "omega-app" deleted</br>
          pod/omega-app replaced

        The POD should now run successfully.

        NOTE:

        It may still run even if the above two syscalls are not added. However, adding the syscalls is required to successfully complete this question.

    </details>


1.  <details>
    <summary>A pod definition file has been created at <code>/root/CKS/simple-pod.yaml</code>. Using the <code>kubesec</code> tool, generate a report for this pod definition file and fix the major issues so that the subsequent scan report no longer fails.</summary>

    Once done, generate the report again and save it to the file `/root/CKS/kubesec-report.txt`

    ---

    1.  Find the issues.

        We can just run the scan fully and look for issues

        ```bash
        kubesec scan /root/CKS/simple-pod.yaml
        ```

        OR, use JQ to give us the critical issues directly:

        ```bash
        kubesec scan /root/CKS/simple-pod.yaml | jq '.[] | .scoring.critical'
        ```

        It tells us the following is an issue

        ```
        containers[] .securityContext .capabilities .add == SYS_ADMIN
        ```

    1. Remove the `SYS_ADMIN` capability from the container for the simple-webapp-1 pod in the POD definition file and re-run the scan.

        ```
        kubesec scan /root/CKS/simple-pod.yaml
        ```

        The fixed report should PASS with a message like this:

        ```json
        [
          {
            "object": "Pod/simple-webapp-1.default",
            "valid": true,
            "fileName": "/root/CKS/simple-pod.yaml",
            "message": "Passed with a score of 0 points",
            "score": 0,
        ```

    1. Save the passing report as directed

        ```
        kubesec scan /root/CKS/simple-pod.yaml > /root/CKS/kubesec-report.txt
        ```

    </details>

1.  <details>
    <summary>Create a new pod called <code>secure-nginx-pod</code> in the <code>seth</code> namespace. Use one of the images from the below which has a least number of <code>CRITICAL</code> vulnerabilities.</summary>

    1. nginx
    1. nginx:1.19
    1. nginx:1.17
    1. nginx:1.20
    1. gcr.io/google-containers/nginx
    1. bitnami/jenkins:latest

    ---

    1. Run trivy image scan on each of the images and check which one has the *least* HIGH or CRITICAL vulnerabilities. Use `grep` to filter out most of the output. The `Total` line is what we really care about

        ```bash
        trivy i nginx | grep Total
        ```

        Repeat the above for remaining images

    1. Create the pod (use correct image found above)

        ```
        kubectl -n seth run secure-nginx-pod --image nginx:alpine
        ```
  </details>



# Mock Exam 1

  - Take me to the [Mock Exam 1](https://kodekloud.com/topic/lab-mock-exam-1-3/)

Solutions for lab - Mock Exam 1:

With questions where you need to modify API server, you can use [this resource](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md) to diagnose a failure of the API server to restart.


1.  <details>
    <summary>A pod has been created in the <code>omni</code> namespace. However, there are a couple of issues with it.</summary>

    1. The pod has been created with more permissions than it needs.
    1. It allows read access in the directory `/usr/share/nginx/html/internal` causing an Internal Site to be accessed publicly.

    To check this, click on the button called Site (above the terminal) and add /internal/ to the end of the URL.
    Use the below recommendations to fix this.

    1. Use the AppArmor profile created at `/etc/apparmor.d/frontend` to restrict the internal site.
    1. There are several service accounts created in the `omni` namespace. Apply the principle of least privilege and use the service account with the minimum privileges (excluding the `default` service account).
    1. Once the pod is recreated with the correct service account, delete the other unused service accounts in `omni` namespace (excluding the `default` service account).

    ---

    1.  Use the `omni` namespace to save on typing

          ```
          k config set-context --current --namespace omni
          ```

    1.  <details>
        <summary>AppArmor Profile</summary>

        Load the AppArmor profile into the kernel

          ```bash
          apparmor_parser -q /etc/apparmor.d/frontend
          ```
        </details>

    1.  <details>
        <summary>Service Account</summary>

        To find the service account with the least privileges, we need to examine the roles that are bound to these service acccounts. This will determine what privilege they have.

        1. Find the service accounts

            ```
            k get sa
            ```

            There are 3 service accounts exculding the `default`. These are the ones we are concerned with.

        1.  Find the bindings

            ```
             k get rolebindings
            ```

            Notice there are 2 bindings, to the roles `fe` and `frontend`

        1.  Examine permissions of roles

                ```
                k describe role fe
                k describe role frontend
                ```

        1.  See which service accounts these roles are bound to

                ```
                k describe rolebinding fe
                k describe rolebinding frontend
                ```

            Notice that these roles are bound to service accounts `fe` and `frontend` respectively. No role is bound to service account `frontend-default`. This means that this service account is the one with least privilege by virtue of the fact that it has _no_ binding and therefore _no_ permissions at all.

        1. Recreate the pod with the correct service account, and also apply the AppArmor profile

                ```yaml
                apiVersion: v1
                kind: Pod
                metadata:
                  annotations:
                    container.apparmor.security.beta.kubernetes.io/nginx: localhost/restricted-frontend # Apply profile 'restricted-fronend' on 'nginx' container
                  labels:
                    run: nginx
                  name: frontend-site
                  namespace: omni
                spec:
                  serviceAccount: frontend-default # Use the service account with least privileges
                  containers:
                  - image: nginx:alpine
                    name: nginx
                    volumeMounts:
                    - mountPath: /usr/share/nginx/html
                      name: test-volume
                  volumes:
                  - name: test-volume
                    hostPath:
                      path: /data/pages
                      type: Directory
                ```
        </details>

    1.  <details>
        <summary>Delete the unused service accounts in the <code>omni</code> namespace.</summary>

          ```bash
          kubectl -n omni delete sa frontend
          kubectl -n omni delete sa fe
          ```

        </details>

   </details>

2.  <details>
    <summary>A pod has been created in the <code>orion</code> namespace. It uses secrets as environment variables. Extract the decoded secret for the <code>CONNECTOR_PASSWORD</code> and place it under <code>/root/CKS/secrets/CONNECTOR_PASSWORD</code>.</summary>


    You are not done, instead of using secrets as an environment variable, mount the secret as a read-only volume at path `/mnt/connector/password` that can be then used by the application inside.

    ---

    1.  <details>
        <summary>Extract the secret</summary>

        ```bash
        mkdir -p /root/CKS/secrets/
        kubectl -n orion get secrets a-safe-secret -o jsonpath='{.data.CONNECTOR_PASSWORD}' | base64 -d > /root/CKS/secrets/CONNECTOR_PASSWORD
        ```

        </details>

    1.  <details>
        <summary>Mount the secret</summary>

        Recreate the pod, mounting the secret as a read-only volume at the given path

          ```yaml
          apiVersion: v1
          kind: Pod
          metadata:
            labels:
              name: app-xyz
            name: app-xyz
            namespace: orion
          spec:
            containers:
            - image: nginx
              name: app-xyz
              ports:
              - containerPort: 3306
              volumeMounts:
              - name: secret-volume
                mountPath: /mnt/connector/password
                readOnly: true
            volumes:
            - name: secret-volume
              secret:
                secretName: a-safe-secret
          ```
        </details>

    </details>


3.  <details>
    <summary>A number of pods have been created in the <code>delta</code> namespace. Using the trivy tool, which has been installed on the controlplane, identify and delete pods <i>except</i> the one with least number of <code>CRITICAL</code> level vulnerabilities.</summary>

    1.  <details>
        <summary>List pods with images for reference</summary>

        ```
        k get pods -n delta -o custom-columns='NAME:.spec.containers[0].name,IMAGE:.spec.containers[0].image'
        ```
        </details>

    1.  <details>
        <summary>Scan each image using <code>trivy image scan</code></summary>

        For each image, replace `<image-name>` with image from the step above and run the command:

        ```bash
        trivy i --severity CRITICAL <image-name> | grep Total
        ```

        Or, do it using a one-liner for loop.

        ```bash
        for i in $(kubectl -n delta get pods -o json | jq -r '.items[].spec.containers[].image') ; do echo $i ; trivy i --severity CRITICAL $i 2>&1 | grep Total ; done
        ```

        </details>

    1.  <details>
        <summary>Delete vulnerable pods</summary>
        If the image has HIGH or CRITICAL vulnerabilities, delete the associated pod.

        Notice that image `httpd:2-alpine` has zero vulnerabilities, so we must delete the pods that _do not_ use this image

        ```bash
        kubectl -n delta delete pod simple-webapp-1
        kubectl -n delta delete pod simple-webapp-3
        kubectl -n delta delete pod simple-webapp-4
        ```

        </details>

  </details>

4.  <details>
    <summary>Create a new pod called <code>audit-nginx</code> in the default namespace using the <code>nginx</code> image. Secure the syscalls that this pod can use by using the <code>audit.json</code> seccomp profile in the pod's security context.</summary>

    The `audit.json` is provided at `/root/CKS` directory. Make sure to move it under the `profiles` directory inside the default seccomp directory before creating the pod

    ---

    1.  <details>
        <summary>Place <code>audit.json</code> into the default seccomp directory.</summary>

        Know that this directory is in kubelet's configuration directory which ia nomally `/var/lib/kubelet`. You can verify this by looking for where it loads its config file from

        ```bash
        ps aux | grep kubelet | grep -- --config
        ```
        Copy the `audit.json` seccomp profile to `/var/lib/kubelet/seccomp/profiles`:

        ```bash
        cp /root/CKS/audit.json /var/lib/kubelet/seccomp/profiles
        ```

        </details>

    1.  <details>
        <summary>Create the pod</summary>

        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            run: nginx
          name: audit-nginx
          namespace : default
        spec:
          securityContext:
            seccompProfile:
              type: Localhost
              localhostProfile: profiles/audit.json
          containers:
          - image: nginx
            name: nginx
        ```
      </details>
  </details>


5.  <details>
    <summary>The CIS Benchmark report for the <code>Controller Manager</code> and <code>Scheduler</code> is available at the tab called <code>CIS Report 1</code>.</summary>

    Inspect this report and fix the issues reported as `FAIL`.

    ---

    1.  <details>
        <summary>Examine report</summary>

        Click on `CIS Reoprt 1` above the terminal

        Note the failures at 1.3.2 and 1.4.1

        </details>

    1.  <details>
        <summary>Fix issues</summary>

        For both `kube-controller-manager` and `kube-scheduler`, edit the static manifest file in `/etc/kubernetes/manifests` and add the following to the command arguments:

          ```yaml
              - --profiling=false
          ```

        Make sure both pods restart

        </details>

   </details>


6.  <details>
    <summary>There is something suspicious happening with one of the pods running an <code>httpd</code> image in this cluster.</br>The Falco service shows frequent alerts that start with: <code>File below a known binary directory opened for writing</code>.</summary>

    Identify the rule causing this alert and update it as per the below requirements:

    1. Output should be displayed as: `CRITICAL File below a known binary directory opened for writing (user_id=user_id file_updated=file_name command=command_that_was_run)`
    1. Alerts are logged to `/opt/security_incidents/alerts.log`

    Do not update the default rules file directly. Rather use the `falco_rules.local.yaml` file to override.

    Note: Once the alert has been updated, you may have to wait for up to a minute for the alerts to be written to the new log location.

    ---

    1.  <details>
        <summary>Create <code>/opt/security_incidents</code></summary>

          ```bash
          mkdir -p /opt/security_incidents
          ```

        </details>

    1.  <details>
        <summary>Enable file_output in <code>/etc/falco/falco.yaml</code></summary>

          ```yaml
          file_output:
            enabled: true
            keep_alive: false
            filename: /opt/security_incidents/alerts.log
          ```

        </details>

    1.  <details>
        <summary>Add the updated rule under the <code>/etc/falco/falco_rules.local.yaml</code></summary>

        Find the relevant rule in `falco_rules.yaml`, copy it, paste it into `falco_rules.local.yaml` and then modify it to get the requested output:

        Refer to the field reference: https://falco.org/docs/reference/rules/supported-fields/

          ```yaml
          - rule: Write below binary dir
            desc: an attempt to write to any file below a set of binary directories
            condition: >
              bin_dir and evt.dir = < and open_write
              and not package_mgmt_procs
              and not exe_running_docker_save
              and not python_running_get_pip
              and not python_running_ms_oms
              and not user_known_write_below_binary_dir_activities
            output: >
              File below a known binary directory opened for writing (user_id=%user.uid file_updated=%fd.name command=%proc.cmdline)
            priority: CRITICAL
            tags: [filesystem, mitre_persistence]
          ```

          </details>

    1.  <details>
        <summary>To perform hot-reload falco use `kill -1` (SIGHUP) on controlplane node</summary>

          ```bash
          kill -1 $(pidof falco)
          ```

        </details>

    1.  <details>
        <summary>Verify falco is running, i.e. you didn't make some syntax error that crashed it</summary>

          ```bash
          systemctl status falco
          ```

        </details>


    1.  <details>
        <summary>Check the new log file. It may take up to a minute for events to be logged.</summary>

          ```bash
          cat /opt/security_incidents/alerts.log
          ```

        </details>

  </details>


7.  <details>
    <summary>A pod called <code>busy-rx100</code> has been created in the <code>production</code> namespace. Secure the pod by recreating it using the <code>runtimeClass</code> called <code>gvisor</code>. You may delete and recreate the pod.</summary>

    Simply recreate the pod using the YAML file as below. We onlt need to add `runtimeClassName`

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: busy-rx100
      name: busy-rx100
      namespace: production
    spec:
      runtimeClassName: gvisor
      containers:
      - image: nginx
        name: busy-rx100
    ```

    Note that the pod may not start due to the fact that `gvisor` runtime is not installed on this system. That's OK as what is being marked is that the pod YAML is correct.

    </details>


8.  <details>
    <summary>We need to make sure that when pods are created in this cluster, they cannot use the latest image tag, irrespective of the repository being used.</summary>

    To achieve this, a simple Admission Webhook Server has been developed and deployed. A service called image-bouncer-webhook is exposed in the cluster internally. This Webhook server ensures that the developers of the team cannot use the latest image tag. Make use of the following specs to integrate it with the cluster using an ImagePolicyWebhook:


    1. Create a new admission configuration file at /etc/admission-controllers/admission-configuration.yaml
    1. The kubeconfig file with the credentials to connect to the webhook server is located at `/root/CKS/ImagePolicy/admission-kubeconfig.yaml`. Note: The directory `/root/CKS/ImagePolicy/` has already been mounted on the kube-apiserver at path `/etc/admission-controllers` so use this path to store the admission configuration.
    1. Make sure that if the latest tag is used, the request must be rejected at all times.
    1. Enable the Admission Controller.

    ---

    1.  <details>
        <summary>Create the admission-configuration inside <code>/root/CKS/ImagePolicy</code> directory as <code>admission-configuration.yaml</code></summary>

          ```yaml
          apiVersion: apiserver.config.k8s.io/v1
          kind: AdmissionConfiguration
          plugins:
          - name: ImagePolicyWebhook
            configuration:
              imagePolicy:
                kubeConfigFile: /etc/admission-controllers/admission-kubeconfig.yaml
                allowTTL: 50
                denyTTL: 50
                retryBackoff: 500
                defaultAllow: false
          ```

          Just create the file. You cannot apply an `AdmissionConfiguration` with kubectl. It's configuration, not a resource!


        Note that the `/root/CKS/ImagePolicy` is mounted at the path `/etc/admission-controllers` directory in the kube-apiserver. So, you can directly place the files under `/root/CKS/ImagePolicy`.<br/>Snippet of the volume and volumeMounts (Note these are _already present_ in apiserver manifest as shown below, so you do not need to add them)

          ```yaml
          containers:
          - # other stuff omitted for brevity
            volumeMounts:
            - mountPath: /etc/admission-controllers
                name: admission-controllers
                readOnly: true
          volumes:
          - hostPath:
              path: /root/CKS/ImagePolicy/
              type: DirectoryOrCreate
            name: admission-controllers
          ```

        </details>

    1.  <details>
        <summary>Update the kube-apiserver command flags and add <code>ImagePolicyWebhook</code> to the <code>enable-admission-plugins</code> flag</summary>

          ```yaml
              - --admission-control-config-file=/etc/admission-controllers/admission-configuration.yaml
              - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
          ```

        </details>

    1. Wait for the API server to restart. May take up to a minute.

        You can use the folloowing command to monitor the containers

        ```bash
        watch crictl ps
        ```

        `CTRL + C` exits the watch.

    1.  <details>
        <summary>Finally, update the pod with the correct image</summary>

          ```bash
          kubectl set image -n magnum pods/app-0403 app-0403=gcr.io/google-containers/busybox:1.27
          ```

        </details>

    </details>

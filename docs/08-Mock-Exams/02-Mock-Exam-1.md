# Mock Exam 1
  - Take me to [Mock Exam 1](https://kodekloud.com/courses/1378608/lectures/31704780)

In this section, we will take a look at Mock Exam 1

- 1
  <details>

  ```

  AppArmor Profile: First load the AppArmor module to the Kernel   
  Run on Node01    
  $ apparmor_parser -q /etc/apparmor.d/frontend  
  Service Account: The pod should use the service account called 'frontend-default' as it has the least   privileges of all the service accounts in the 'omni' namespace (excluding default)
  The other service accounts, 'fe' and 'frontend' have additional permissions (check the roles and rolebindings associated with these accounts)
  Use the below YAML File to re-create the pod.
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      container.apparmor.security.beta.kubernetes.io/nginx: localhost/restricted-frontend #Apply profile 'restricted-fronend' on 'nginx' container
    labels:
      run: nginx
    name: frontend-site
    namespace: omni
  spec:
    serviceAccount: frontend-default #Use the service account with least privileges
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
  Delete the unused service accounts in the 'omni' namespace.
  $ kubectl -n omni delete sa frontend
  $ kubectl -n omni delete sa fe
  ```
  </details>


- 2

  <details>

  ```
  To extract the secret
  Run:
  $mkdir -p /root/CKS/secrets/
  $kubectl -n orion get secrets a-safe-secret -o jsonpath='{.data.CONNECTOR_PASSWORD}' | base64 --decode >/root/CKS/secrets/CONNECTOR_PASSWORD

  One way that is more secure to distribute secrets is to mount it as a read-only volume.

  Create pod using:

  apiVersion: v1
  kind: Pod
  metadata:
      labels:
          name: app-xyz
      name: app-xyz
      namespace: orion
  spec:
      containers:
          -
              image: nginx
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


- 3

  <details>

  ```
  Get all the images of pods running in the 'delta' namespace:
  $ kubectl -n delta get pods -o json | jq -r '.items[].spec.containers[].image'
  Scan each image using 'trivy image scan'. Example:
  $ trivy image --severity HIGH,CRITICAL kodekloud/webapp-delayed-start
  If the image has HIGH or CRITICAL vulnerabilities, delete the associated pod.
  For example, if 'kodekloud/webapp-delayed-start', 'httpd' and 'nginx:1.16' have these vulnerabilities:
  $ kubectl -n delta delete pod simple-webapp-1
  $ kubectl -n delta delete pod simple-webapp-3
  $ kubectl -n delta delete pod simple-webapp-4
  ```
  </details>


- 4

  <details>

  ```
  Copy the audit.json seccomp profile to /var/lib/kubelet/seccomp/profiles in node01:
  $ ssh node01
  $ cp /root/CKS/audit.json /var/lib/kubelet/seccomp/profiles
  Recreate the pod using the below YAML File
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      run: nginx
    name: audit-nginx
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


- 5

   <details>
   ```
   # The fixes are mentioned in the same report.
   # Update the kube-apiserver static pod definition file under "/etc/kubernetes/manifests/kube-apiserver.yaml" as per the recommendations.
   # Make sure that --authorization-mode=Node,RBAC
   ```

   </details>


- 6
   <details>
    ```
    # Create /opt/security_incidents on node01

          $ mkdir -p /opt/security_incidents
          #Enable file_output in /etc/falco/falco.yaml

          file_output:
            enabled: true
            keep_alive: false
            filename: /opt/security_incidents/alerts.log

      #Add the updated rule under the /etc/falco/falco_rules.local.yaml and hot reload the #Falco service on node01:

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
           File below a known binary directory opened for writing (user=%user.name file_updated=%fd.name command=%proc.cmdline)
         priority: CRITICAL
         tags: [filesystem, mitre_persistence]

         # To perform hot-reload falco use 'kill -1 /SIGHUP' on node01:

         $ kill -1 $(cat /var/run/falco.pid)
   ```
   </details>
- 7

   <details>

   ```
   # Use the YAML file as below:

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
           -
               image: nginx
               name: busy-rx100
   ```
   </details>


- 8



 <details>

```
   # Create the below admission-configuration inside /root/CKS/ImagePolicy directory in the controlplane  

   # use this YAML file:

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

   #The /root/CKS/ImagePolicy is mounted at the path /etc/admission-controllers directory in the kube-apiserver. So, you can directly place the files under /root/CKS/ImagePolicy.
   #---snippet of the volume and volumeMounts (already added to apiserver config)
     containers:
     .
     .
     .
     volumeMounts:
     - mountPath: /etc/admission-controllers
         name: admission-controllers
         readOnly: true
     volumes:
     - hostPath:
         path: /root/CKS/ImagePolicy/
         type: DirectoryOrCreate
       name: admission-controllers
   # update the kube-apiserver command flags and add ImagePolicyWebhook to the enable-admission-plugins flag

   - --admission-control-config-file=/etc/admission-controllers/admission-configuration.yaml
   - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
   ```
   </details>

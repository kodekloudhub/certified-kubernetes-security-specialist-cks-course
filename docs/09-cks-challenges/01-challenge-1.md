# CKS Challenge 1

[Take me to the lab!](https://learn.kodekloud.com/user/courses/cks-challenges/module/624cd49b-715f-45e8-9959-372425b771a6/lesson/4e781840-cc73-46d2-8608-f4608be7f005)

Please note that the competition status for CKS Challenges is ended. Please do not submit a solution. It will not be scored.

# Challenge

There are 6 images listed in the diagram. Using `Aquasec Trivy` (which is already installed on the controlplane node), identify the image that has the least number of critical vulnerabilities and use it to deploy the `alpha-xyz` deployment.

Secure this deployment by enforcing the AppArmor profile called `custom-nginx`.

Expose this deployment with a ClusterIP type service and make sure that only incoming connections from the pod called `middleware` is accepted and everything else is rejected.

Click on each icon (in the lab) to see more details. Once done, click the Check button to test your work.

![Diagram](../../images/challenge-1.png)

Do the tasks in this order:

1.  <details>
    <summary>namespace</summary>

    All the action is taking place in the `alpha` namespace

    ```
    kubectl config set-context --current --namespace alpha
    ```

1.  <details>
    <summary>alpha-pv</summary>

    * A persistentVolume called `alpha-pv` has already been created. Do not modify it. Inspect the parameters used to create it.

    <details>
    <summary>Reveal</summary>

    ```bash
    kubectl describe pv alpha-pv
    ```

    Note `StorageClass`, `Access Modes`, `Capacity`, `VolumeMode`

    </details>
    </details>

1.  <details>
    <summary>alpha-pvc</summary>

    * `alpha-pvc` should be bound to `alpha-pv`. Delete and Re-create it if necessary.

    <details>
    <summary>Reveal</summary>

    ```
    kubectl get pvc alpha-pvc
    ```

    > Status is pending, i.e it wont bind.

    Delete the PVC and recreate it with values for storage class, access modes and capacity matching those of the PV

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
    name: alpha-pvc
    namespace: alpha
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 1Gi
      storageClassName: local-storage
      volumeMode: Filesystem
    ```

    </details>
    </details>

1.  <details>
    <summary>images</summary>

    * Permitted images are: `docker.io/library/nginx:alpine`, `docker.io/bitnami/nginx`, `docker.io/library/nginx:1.13`, `docker.io/library/nginx:1.17`, `docker.io/library/nginx:1.16` and `docker.io/library/nginx:1.14`. Use `trivy` to find the image with the least number of `CRITICAL` vulnerabilities.

    <details>
    <summary>Reveal</summary>

    1. Inspect all images

        ```
        crictl image ls
        ```

        Note there are additional images other than those stated

    1.  Loop over the images we want (by filtering out those we don't), and trivy them getting the information we need

        ```bash
        for i in $(crictl image -f reference=nginx -o json | jq -r .images[].repoTags[] | grep -v "docker.io/library/nginx:latest")
        do
            echo -n "$i "
            trivy i -s CRITICAL $i 2>&1 | grep Total | awk '{print $2}'
        done
        ```

        We can see that `nginx:alpine` has the least (zero) criticals, which is kind of as expected! We will use this image when we come to deploy the pod later.

    </details>
    </details>


1.  <details>
    <summary>custom-nginx</summary>

    * Move the AppArmor profile `/root/usr.sbin.nginx` to `/etc/apparmor.d/usr.sbin.nginx` on the controlplane node
    * Load the AppArmor profile called `custom-nginx` and ensure it is enforced.

    <details>
    <summary>Reveal</summary>

    1.  ```bash
        mv /root/usr.sbin.nginx /etc/apparmor.d/usr.sbin.nginx
        ```
    1.  ```bash
        apparmor_parser /etc/apparmor.d/usr.sbin.nginx
        ```

    </details>
    </details>

1.  <details>
    <summary>alpha-xyz</summary>

    * Create a deployment called `alpha-xyz` that uses the image with the least 'CRITICAL' vulnerabilities? (Use the sample YAML file located at `/root/alpha-xyz.yaml` to create the deployment. Please make sure to use the same names and labels specified in this sample YAML file!)
    * Deployment has exactly `1` ready replica
    * `data-volume` is mounted at `/usr/share/nginx/html` on the pod
    * `alpha-xyz` deployment uses the `custom-nginx` apparmor profile (applied to container called `nginx`). Note that this task is revealed by clicking the arrow between `custom-nginx` and `alpha-xyz`

    <details>
    <summary>Reveal</summary>


    Edit the given file `/root/alpha-xyz.yaml` and fill in the necessary properties. We need to use the PVC from step 3, the image determined in step 4 and the apparmor profile from step 5

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      creationTimestamp: null
      labels:
        app: alpha-xyz
      name: alpha-xyz
      namespace: alpha
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: alpha-xyz
      strategy: {}
      template:
        metadata:
          labels:
            app: alpha-xyz
        spec:
          volumes:
          - name: data-volume
            persistentVolumeClaim:
              claimName: alpha-pvc
          containers:
          - image: nginx:alpine
            name: nginx
            securityContext:
              appArmorProfile:
                type: Localhost
                localhostProfile: custom-nginx
            volumeMounts:
            - name: data-volume
              mountPath: /usr/share/nginx/html
    ```

    ```bash
    kubectl apply -f /root/alpha-xyz.yaml
    ```

    </details>
    </details>

1.  <details>
    <summary>alpha-svc</summary>

    * Expose the `alpha-xyz` as a `ClusterIP` type service called `alpha-svc`
    * `alpha-svc` should be exposed on `port: 80` and `targetPort: 80`

    <details>
    <summary>Reveal</summary>

    ```bash
    kubectl expose deployment alpha-xyz --type ClusterIP --name alpha-svc --port 80 --target-port 80
    ```

    </details>
    </details>

1.  <details>
    <summary>restrict-inbound</summary>

    * Create a NetworkPolicy called `restrict-inbound` in the `alpha` namespace
    * Policy Type = `Ingress`
    * Inbound access only allowed from the pod called `middleware` with label `app=middleware`
    * Inbound access only allowed to TCP port 80 on pods matching the policy
    * Policy should be only applied on pods with label `app=alpha-xyz`. This task is revealed by clicking the arrow between `restrict-inbound` and `alpha-xyz`

    <details>
    <summary>Reveal</summary>

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: NetworkPolicy
    metadata:
      name: restrict-inbound
      namespace: alpha
    spec:
      podSelector:                  # Policy should be only applied on pods with label app=alpha-xyz
        matchLabels:
          app: alpha-xyz
      policyTypes:
        - Ingress
      ingress:
        - from:
            - podSelector:          # Inbound access only allowed from the pod called middleware with label app=middleware
                matchLabels:
                  app: middleware
          ports:                    # Inbound access only allowed to TCP port 80 on pods matching the policy
            - port: 80
    ```

    Apply this policy

    </details>
    </details>


Once all the above tasks are completed, click the `Check` button.

# Automate the lab in a single script!

As DevOps engineers, we love everything to be automated!

<details>
<summary>Automation Script</summary>

Paste this entire script to the lab terminal, sit back and enjoy!<br/>
When the script completes, you can press the `Check` button and the lab will be complete!


```bash
# CKS challenge 1
{
start_time=$(date '+%s')

# Set namespace
kubectl config set-context --current --namespace alpha

# Fix PVC
kubectl delete pvc alpha-pvc

cat <<EOF | kubectl create -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: alpha-pvc
  namespace: alpha
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  storageClassName: local-storage
  volumeMode: Filesystem
EOF

kubectl wait --for=jsonpath='{.status.phase}'=Bound pvc/alpha-pvc --timeout=30s

# Find image with least vulnerabilites
img=''
vuln=10000

for i in $(crictl image -f reference=nginx -o json | jq -r .images[].repoTags[] | grep -v "docker.io/library/nginx:latest")
do
    echo "Trivy - $i"
    crit=$(trivy i -s CRITICAL $i  2>&1 | grep Total | awk '{print $2}')
    [ $crit -lt $vuln ] && vuln=$crit && img=$i
done

echo "$img - $vuln critical."

# Set up apparmor
mv /root/usr.sbin.nginx /etc/apparmor.d/usr.sbin.nginx
apparmor_parser /etc/apparmor.d/usr.sbin.nginx

# Create deployment with selected image

cat << EOF | kubectl create -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: alpha-xyz
  name: alpha-xyz
  namespace: alpha
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alpha-xyz
  strategy: {}
  template:
    metadata:
      labels:
        app: alpha-xyz
    spec:
      volumes:
      - name: data-volume
        persistentVolumeClaim:
          claimName: alpha-pvc
      containers:
      - image: $img
        name: nginx
        securityContext:
          appArmorProfile:
            type: Localhost
            localhostProfile: custom-nginx
        volumeMounts:
        - name: data-volume
          mountPath: /usr/share/nginx/html
EOF

kubectl wait deployment -n alpha alpha-xyz --for condition=Available=True --timeout=30s

# Expose deployment
kubectl expose deployment alpha-xyz --type ClusterIP --name alpha-svc --port 80 --target-port 80

# Create netpol
cat << EOF | kubectl create -f -

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-inbound
  namespace: alpha
spec:
  podSelector:
    matchLabels:
      app: alpha-xyz
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: middleware
      ports:
        - port: 80
EOF

end_time=$(date '+%s')
duration=$(( end_time - start_time ))
echo "Complete in ${duration}s"

}
```

</details>

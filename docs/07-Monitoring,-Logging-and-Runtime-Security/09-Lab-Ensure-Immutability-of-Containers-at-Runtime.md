# Lab - Ensure Immutability of Containers at Runtime

  - Take me to the [Lab](https://kodekloud.com/topic/labs-ensure-immutability-of-containers-at-runtime/)

Solutions to Lab - Ensure Immutability of Containers at Runtime:

1.
<details>

```
Check if the pods are running with read-only root and do not use elevated privileges.

Answer: "All of them"

```
</details>


2.
<details>

```
It can write to the root filesystem
```
</details>

3.
<details>

```
# Use securityContext in the container section and add readOnlyRootFilesystem to true.

apiVersion: v1
kind: Pod
metadata:
  labels:
    name: triton
    namespace: alpha
  name: triton
  namespace: alpha
spec:
  containers:
  - image: httpd
    name: triton
    securityContext:
      readOnlyRootFilesystem: true
```
</details>

4.
<details>

```
CrashLoopBackOff
```
</details>

5.
<details>

```
# Inspect the logs to find out the reason.

/usr/local/apache2/logs is read-only
```
</details>


6.
<details>

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: triton
  name: triton
  namespace: alpha
spec:
  containers:
  - image: httpd
    name: triton
    securityContext:
      readOnlyRootFilesystem: true
    volumeMounts:
    - mountPath: /usr/local/apache2/logs
      name: log-volume
  volumes:
  - name: log-volume
    emptyDir: {}
```
</details>


7.
<details>

```
apiVersion: v1
kind: Pod
metadata:
  name: grimsby
  namespace: alpha
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
  volumes:
    - name: demo-volume
      emptyDir: {}
  containers:
    - name: sec-ctx-demo
      image: busybox
      command: [ "sh", "-c", "sleep 5h" ]
      volumeMounts:
        - name: demo-volume
          mountPath: /data/demo
```
</details>

8.
<details>

```
kubectl -n alpha delete pod solaris
```
</details>



# Lab - Ensure Immutability of Containers at Runtime

  - Take me to [Lab](https://kodekloud.com/topic/labs-ensure-immutability-of-containers-at-runtime/)

Solutions to Lab Ensure Immutability of Containers at Runtime.

- 1

  <details>
  ```
  Check if the pods are running with read-only root and do not use elevated privileges.

  Answer: "All of them"

  ```
  </details>
- 2

    Answer **`it can write to the root filesystem`**


- 3

  - Use securityContext in the container section and add readOnlyRootFilesystem to true.




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


- 4

    Answer: **`CrashLoopBackOff`**

- 5

    - Inspect the logs to find out the reason.

    Answer: **`/usr/local/apache2/logs is read-only`**



- 6


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


- 7

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


- 8

    Answer: **`kubectl -n alpha delete pod solaris`**


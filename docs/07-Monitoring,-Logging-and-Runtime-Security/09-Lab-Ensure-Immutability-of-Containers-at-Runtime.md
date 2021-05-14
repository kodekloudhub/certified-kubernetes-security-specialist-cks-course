# Lab Ensure Immutability of Containers at Runtime
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704777)

Solutions to Lab Ensure Immutability of Containers at Runtime

- We have deployed a few pods in the alpha namespace. Which one of them cannot be considered as an immutable pod?

  <details>
  ```
  Check if the pods are running with read-only root and do not use elevated privileges.

  Answer: "All of them"

  ```
  </details>


- Why isn't the pod called triton considered to be immutable?

    Answer **`it can write to the root filesystem`**


- Let's fix that. Make sure that the triton pod uses a read-only root filesystem.

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


- What is the status of the triton pod now?

    Answer: **`CrashLoopBackOff`**

- Why is it in this state?

    - Inspect the logs to find out the reason.

    Answer: **`/usr/local/apache2/logs is read-only`**



- Add a volume of type emptyDir to be mounted at the path /usr/local/apache2/logs inside the container running in the triton pod. Use the below specs:

  - volume-name: log-volume
  - mountPath: /usr/local/apache2/logs
  - Type: emptyDir



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


- Another pod called grimsby has been created inside the alpha namespace. Currently it runs as the root user. Update the pod so that it runs with the uid of 1000 and gid of 3000.

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


- Using privileged containers is a huge security risk! Delete the solaris pod entirely from the cluster.

    Answer: **`kubectl -n alpha delete pod solaris`**

# Lab AppArmor
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704459)

Solutions Lab AppArmor

- AppArmor is in beta **`beta`** state as of version 1.20

- Is the AppArmor module loaded in node01?
  <details>
  ```
  Run
  $ ssh node01
  $ aa-status
  ```
  </details>

- How many AppArmor profiles have been loaded in total currently?
  <details>
    ```

    Run
    $ ssh node01

    $ aa-status

    52
    ```
  </details>
- How many AppArmor profiles have been loaded in enforce mode currently?
  <details>
    ```

    Run
    $ ssh node01

    $ aa-status

    15
    ```
  </details>
- A pod called nginx has been deployed in the default namespace.
What is the status of this pod?
  <details>
    ```

    Run

    Exit from node01 using

    $ exit

    Then get the pods using and examine the status

    $ kubectl get pods

    Blocked
    ```
  </details>
- Why is this pod in a blocked state?
  <details>
    ```

    Inspect the error message for this pod by running

    $ kubectl describe pod nginx

    AppArmor profile not loaded
    ```
  </details>
- What is the name of the AppArmor profile used by this pod?
  <details>
    ```

    Inspect the failure message for this pod by running

    $ kubectl describe pod nginx

    Custom nginx
    ```
  </details>
- Load the AppArmor profile called custom-nginx on node01 and make sure that it is in enforced mode.
The profile file is called usr.sbin.nginx located in the default AppArmor profiles directory.
  <details>
  ```

  $ ssh node01

  $ apparmor_parser -q /etc/apparmor.d/usr.sbin.nginx

  Custom nginx
  ```
  </details>
- Recreate the nginx pod. The pod definition file is created at /root/CKS/custom-nginx.yaml
  <details>
  ```

  $ exit

  $ kubectl apply -f /root/CKS/custom-nginx.yaml

  ```
  </details>
- Which tabs are you able to access?
  <details>
  ```
  Public and restricted site
  ```
  </details>
- Let's fix that. Another profile is created at /etc/apparmor.d/usr.sbin.nginx-updated which prevents reads on the restricted directory inside the container.
Use this AppArmor profile and recreate this container.
  <details>
  ```

  make sure restricted-nginx is loaded by running

  $ ssh node01

  $ aa-status

  Run it using

  $ apparmor_parser -q /etc/apparmor.d/usr.sbin.nginx-updated

  Validate that it is running using

  $ aa-status

  Update the pod YAML file's annotation with the restricted-nginx apparmor profile and then recreate the pod.
  ```
  </details>

# Lab - Kubelet-Security

  - Take me to the [Lab](https://kodekloud.com/topic/labs-kubelet-security/)

Solutions for Lab - Kubelet-Security:

- The **`/var/lib/kubelet/config.yaml`** file contains the kubelet configuration

    <details>

    ```
    Run ps -ef | grep /usr/bin/kubelet and check the value of the --config parameter.
    ```
    </details>


- What is the value set for the rotateCertificates property for the kubelet? **`true`**

    <details>

    ```
    Please check the value of the property rotateCertificates in /var/lib/kubelet/config.yaml
    ```
    </details>


- Which of the below is correct when it comes to the default ports for full access and read-only access for the kubelet?
  **`full access port : 10250, read-only access port: 10255`**

- As per the current configuration, does the kubelet allow requests for anonymous users? **`yes`**

    <details>

    ```
    Check the authentication setting in /var/lib/kubelet/config.yaml
    ```
    </details>


- What kind of authorization is set on the kubelet? **`AlwaysAllow`**

  <details>

  ```
  Check the authorization setting in /var/lib/kubelet/config.yaml
  ```
  </details>


- This shouldn't be allowed. Set the authorization mode to Webhook and restart kubelet. Then call the Pods API again using the command curl -sk https://localhost:10250/pods
    <details>

    ```
    Update authorization setting in /var/lib/kubelet/config.yaml and restart kubelet using systemctl restart kubelet.service
    ```
    </details>


- Disable metrics endpoint on readOnlyPort. After disabling check the metrics API again and verify that it does not display any results. Command: curl -sk http://localhost:10255/metrics

    <details>

    ```
    Set readOnlyPort as 0 in /var/lib/kubelet/config.yaml and restart kubelet  using systemctl restart kubelet.service
    ```
    </details>




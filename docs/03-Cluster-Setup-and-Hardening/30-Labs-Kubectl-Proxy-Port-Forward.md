# Lab - Kubectl-Proxy-&-Port-Forward

  - Take me to the [Lab](https://kodekloud.com/topic/labs-kubectl-proxy-port-forward/)

Solutions for Lab - Kubectl Proxy & Port Forward:

  - **`kubectl`** command line utility can be used to interact with the Kubernetes API server directly.

  - **`~/.kube/config`** file stores the authentication credentials for kubectl.

  - **`both --key and --cert options`** are the options we missing in curl command curl -k https://localhost:6443

  - **`Kubectl proxy`** command is used to start proxy on the controlplane node
  - Call the API endpoint /version of kubectl proxy using curl and redirect the output to the file: /root/kube-proxy-version.json
  Tip: Open a new terminal if you didn't run kubectl proxy as a background service in the previous step
    <details>

    ```
    $ curl 127.0.0.1:8001/version > /root/kube-proxy-version.json
    ```
    </details>
  
  - Call the API endpoint /version of kubectl proxy using curl and redirect the output to the file: /root/kube-proxy-version.json
    <details>
  
    ```
    $ kubectl proxy --port 8002 &
    ```
    </details>
  
  - **`kubectl port-forward`** can forward a local port to a port on the Pod

  - **`kubectl port-forward pod/app 8000:5000`** used to forward port 8000 on the host to port 5000 on the pod app

  - We deployed the nginx app in the default namespace. Wait for a few seconds for the pod to spin up. Forward port 8005 of localhost to port 80 of nginx pods. Run port-forward process in background. Try accessing port 8005 after port forwarding.
    <details>

    ```

    $ kubectl get all

    $ kubectl port-forward pods/{POD_NAME} 8005:80 &

    OR

    $ kubectl port-forward deployment/{DEPLOYMENT_NAME} 8005:80 &

    OR

    $ kubectl port-forward service/{SERVICE_NAME} 8005:80 &

    OR

    $ kubectl port-forward replicaset/{REPLICASET_NAME} 8005:80 &

    then try curl localhost:8005 to check nginx response
    ```
    </details>
  
  - Summary
    <details>
  
    ```
    kubectl port-forward can forward a local port to a port on the Pod and kubectl port forward opens port to target deployment pods.
    ```
    </details>



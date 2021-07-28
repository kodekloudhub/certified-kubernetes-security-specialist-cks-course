# Lab - Use Falco to detect threats

  - Take me to [Lab](https://kodekloud.com/topic/use-falco-to-detect-threats/)

In this section, we will take a look at Lab - Use Falco to detect threats.

- 1

  <details>
    ```
    You will have to check the status of the falco service on both node.
    Or if it deployed as a pod, check if it running on all nodes using
    kubectl get pods --all-namespaces or systemctl status falco
    ```
  </details>  
- 2

    Answer: As Package on the node



- 3
  <details>
    ```

  Use systemctl status falco and inspect the status.

  Answer: Running

    ```
  </details>
- 4
  <details>
  ```
Since falco is running as a service, we can make use of journalctl -u falco on node01 to inspect the events generated.

  ```
  </details>
- 5

  <details>
  ```
  falco uses the configuration file located at /etc/falco/falco.yaml. This can also be see in the journalctl command.
Look for the message that starts with Falco initialized with configuration file...

  Answer: /etc/falco/falco.yaml
  ```
  </details>
- 6

  Answer: **`/etc/falco/custom_rules.yaml`**

- 7

  <details>
  ```
  Check the json_output field. If set to false, the format used is text
  ```
  </details>


- 8

  <details>
  ```
  Rules are read in the order of files in the list. If the same rule is present in all the files, the one in the last file overrides the others.
  ```
  </details>


- 9

  <details>
  ```

        on node01# systemctl status falco  and check the container_id.

         this container_id is a part of `simple-webapp-1` pod and name space critical-apps.

          then on the controlplane, Run  echo "critical-apps,simple-webapp-1" > /root/compromised_pods.txt

  ```
  </details>
- 10

    Answer: **`Error`**

- 11

  Answer: **`apt update`**

- 12

  <details>
  ```
  The output starting with Package management process launched in container... is used by one single rule called Launch Package Management Process in Container.
  ```
  </details>


- 13

    Answer: **`/etc/falco/falco_rules.yaml`**


- 14


      Change the output so that it now prints the events in the following sample format:

      Error Package Management Tools Executed (user=root command=apt update container_id=6b1aeedc093a)

    To override, place the updated rule in /etc/falco/falco_rules.local.yaml. Then reload the Falco configuration and restart the engine without restarting the service.

    Finally, try running kubectl exec simple-webapp-1 -- apt update on the controlplane node and see if the changed rule is seen in the falco logs.

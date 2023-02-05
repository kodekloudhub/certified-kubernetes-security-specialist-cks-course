# Lab - Use Audit Logs to monitor access

  - Take me to the [Lab](https://kodekloud.com/topic/labs-use-audit-logs-to-monitor-access/)

Solutions to Lab - Use Audit Logs to monitor access:

- 1
<details>

```
kube api-server
```
</details>

- 2
<details>

```
No
```
</details>

- 3
<details>

```
ResponseComplete
```
</details>

- 4
<details>

```
RequestResponse
```
</details>

- 5
<details>

```
logs all requests at the metadata level
```
</details>

- 6
  <details>
  <summary>Now enable auditing in this Kubernetes cluster. Create a new policy file and set it to Metadata level and it will only log events based on the below specifications:</summary>

    * Namespace: `prod`
    * Operations: `delete`
    * Resources: `secrets`
    * Log Path: `/var/log/prod-secrets.log`
    * Audit file location: `/etc/kubernetes/prod-audit.yaml`
    * Maximum days to keep the logs: `30`


    1. Create the policy with vi at `/etc/kubernetes/prod-audit.yaml`

        ```yaml
        apiVersion: audit.k8s.io/v1
        kind: Policy
        rules:
        - level: Metadata
          namespaces: ["prod"]
          verbs: ["delete"]
          resources:
          - group: ""
            resources: ["secrets"]
        ```

    1.  Edit the api server manifest and make the changes to add the necessary command line arguments, volumes and mounts.

        1. Add these arguments

            ```yaml
                - --audit-policy-file=/etc/kubernetes/prod-audit.yaml
                - --audit-log-path=/var/log/prod-secrets.log
                - --audit-log-maxage=30
            ```

        1. Add these volumes

            ```yaml
              - name: audit
                hostPath:
                  path: /etc/kubernetes/prod-audit.yaml
                  type: File
              - name: audit-log
                hostPath:
                  path: /var/log/prod-secrets.log
                  type: FileOrCreate
            ```

        1. Add these mounts

            ```yaml
                - mountPath: /etc/kubernetes/prod-audit.yaml
                  name: audit
                  readOnly: true
                - mountPath: /var/log/prod-secrets.log
                  name: audit-log
                  readOnly: false
            ```

        1. Save and exit vi. Wait for apiserver to go down and come back up - can take up to 60 seconds

            ```bash
            watch crictl ps
            ```

            If the api server does not come back up, then [diagnose this](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md).

        1. Test the auditing

            ```bash
            kubectl create secret -n prod generic test --from-literal x=1
            kubectl delete secret -n prod test
            # Wait a few secords
            cat /var/log/prod-secrets.log | jq -C
            ```

  </details>





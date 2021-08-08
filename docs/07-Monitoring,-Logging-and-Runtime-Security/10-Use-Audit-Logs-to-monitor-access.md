# Use Audit Logs to monitor access

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/use-audit-logs-to-monitor-access/)

In this section, we will take a look at `how to use Audit Logs to monitor access`.


- Auditing events would allow us to detect suspicious or abnormal operations within the kubernetes cluster.

- Kubernetes provides auditing by default and it is handled by the kube-apiserver.


- Audit records begin their life cycle inside the kube-apiserver component. Each request on each stage of its execution generates an audit event, which is then pre-processed according to a certain policy and written to a backend. The policy determines what's recorded and the backends persist the records.

- Each request can be recorded with an associated stage. The defined stages are:

  - **`RequestReceived`** - The stage for events generated as soon as the audit handler receives the request, and before it is delegated down the handler chain.

  - **`ResponseStarted`** - Once the response headers are sent, but before the response body is sent. This stage is only generated for long-running requests.

  - **`ResponseComplete`** - The response body has been completed and no more bytes will be sent.

  - **`Panic`** - Events generated when a panic occurred.


###  Record the event if pods inside the prod-namespace are deleted

    apiVersion: audit.k8s.io/v1
    kind: Policy
    omitStages: ["RequestReceived"]
    rules:
      - namespaces: ["prod-namespace"]
        verbs: ["delete"]
        resources:
        - groups: ""
          resources: ["pods"]
          resourceNames: ["webapp-pod "]
        level: RequestResponse

  - **`omitStages`** Don't generate audit events for all requests in the RequestReceived stage.

  - **`namespaces`** is an optional field and if it is unspecified, the policy will consider objects across all namespaces wherever applicable.

  - **`verbs`** field which decides the specific operations on the objects the policy needs to match. This field is optional as well, if it is not declared, every operation, such as create/update and delete will be logged by default.

  - **`resources`** within it,  we can specify multiple kubernetes objects under audit so it is an array of objects.

  - **`level`** field it can accept 4 different values.

    - **`None`** implies that events will not be logged for this specific rule. So in this case no events will be logged if the pod called web-app is deleted in the prod-namespace.

    - **`Metadata`** in this case if the event matches the rule –only the metadata such as the timestamp, user, resources, verbs e.t.c will be logged.

    - **`Request`** to get more details which will include the metadata as well as the request body. This is more verbose compared to the Metadata.

    - **`RequestResponse`** to log the metadata, the request body as well as the response body.




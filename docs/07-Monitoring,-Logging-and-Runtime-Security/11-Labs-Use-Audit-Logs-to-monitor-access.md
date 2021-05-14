# Labs Use Audit Logs to monitor access
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31733999)

Solutions to Labs Use Audit Logs to monitor access

- Which component in the Kubernetes cluster is responsible for handling audit logs?

    Answer: **`kubeapi server`**

- Is auditing enabled at this moment? Check the command used by the kube-apiserver pod to find out. This cluster has been deployed using kubeadm so check accordingly.

    Answer: `No`

- Which stage generates events when a request is complete?

    Answer: `ResponseComplete`


- In an audit policy object, what level would you configure for highest verbosity of logs?

    Answer: `RequestResponse`

- A sample policy file called audit-1.yaml has been created under the root filesystem in the controlplane. Analyze it.

    Which audit events will be logged by this policy?

    Answer: `logs all requests at the metadata level`

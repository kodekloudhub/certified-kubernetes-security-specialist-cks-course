# Lab - OPEN POLICY AGENT (OPA)

  - Take me to the [Lab](https://kodekloud.com/topic/labs-opa/)

Solutions for Lab - OPA:

- OPA stands for **`Open Policy Agent`**.

- Install and run the latest OPA on the system in the background.

  <details>

  ```
  Update VERSION below with the latest version available in the release page
  $ export VERSION=v0.27.1
  $ curl -L -o opa https://github.com/open-policy-agent/opa/releases/download/${VERSION}/opa_linux_amd64
  $ chmod 755 ./opa
  $ ./opa run -s &
  ```
  </details>

- **`8181`** is the default port on which OPA runs.

- **`Rego`** is the language used to write policies in OPA.

- Fix the error in the policy given at `/root/example.rego`

  <details>

  ```
  $ vi /root/example.rego
  Set "default allow = false"
  Run the below command to test policy
  $ ./opa test example.rego

  ```
  </details>

 - Load policy `/root/sample.rego` to OPA with the name samplepolicy.

  <details>

  ```
  Run Below command to import sample.rego in OPA
  $ curl -X PUT --data-binary @sample.rego http://localhost:8181/v1/policies/samplepolicy
  ```
  </details>

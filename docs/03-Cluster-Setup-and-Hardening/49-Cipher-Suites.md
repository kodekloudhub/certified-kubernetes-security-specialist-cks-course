# Securing Control Plane Communications with Ciphers

As we have already covered in previous lectures, communication between clients and API server, and also between the Kubernetes components is by way of Mutual TLS (mTLS). TLS works by use of Public Key Encryption, and the encryption is performed by cryptographic mathematical algorithms known as _ciphers_. Mathematicians discover new ciphers from time to time that are more secure than their predecessors.

Each time a new cipher is discovered, it has to work its way into general usage, that is, that the software libraries that implement encryption need to be updated with the new cipher, whilst remaining compatible with the existing well-known ciphers. These updates have to find their way into all software that makes use of HTTPS (TLS) protocols including, but not limited to

* Browsers
* Web clients (e.g. curl, wget)
* Web servers (e.g. IIS, nginx, apache etc)
* Layer 7 appliances (e.g. AWS Application Load Balancer, Web Application Firewalls)
* Kubernetes components (API server, controller manager, kubelet, scheduler)
* etcd

When a TLS connection is established, the cipher to use is negotiated between the two ends, and usually the strongest possible cipher that both ends know is selected. The ciphers available to each end of the connection depend on how old that software is, and thus which ciphers are known to it.

Most TLS aware software packages, and for the purpose of CKS, this includes all the control plane components and etcd, have the ability to limit which ciphers should be available for negotiation when a connection is being established. Limiting the available ciphers to the newer (stronger) ones prevents older clients that do not have the newer ciphers from establishing a connection which may be able to be compromised due to use of an older (weaker) cipher for which a known exploit is available.

## Ciphers and the Kubernetes Control Plane

All the control plane components (API server, controller manager, kubelet, scheduler) have the following two optional arguments:

* `--tls-min-version` - This argument sets the minimum version of TLS that may be used during connection negotiation.  Possible values: `VersionTLS10`, `VersionTLS11`, `VersionTLS12`, `VersionTLS13`, for TLS 1.0 thru TLS 1.3 respectively. The default is `VersionTLS10`.
* `--tls-cipher-suites` - This argument sets a comma-separated list of cipher suites that may be used during connection negotiation. There are many of these, and the full list may be found on the [api server argument page](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/). If this argument is omitted, the default value is the list provided by the [GoLang cipher suites package](https://go.dev/src/crypto/tls/cipher_suites.go#L53).

`etcd` also has a command line argument to set cipher suites. Thus it is possible to secure api server &rarr; etcd communication to use only specific ciphers that they have in common. You would most likely want to select the newest/strongest.

* `--cipher-suites` - This argument sets a comma-separated list of cipher suites that may be used during connection negotiation. If this argument is omitted, the default value is the list provided by the [GoLang cipher suites package](https://go.dev/src/crypto/tls/cipher_suites.go#L53).

Be aware that not all combinations of cipher suites and TLS versions are compatible with each other. If you set `--tls-min-version` to `VersionTLS13`, there will be certain ciphers that can't be used so explicitly specifying an incompatible cipher with `--tls-cipher-suites` would cause API server to not come back up.

Should you encounter an exam question relating to ciphers, you will be told which one(s) to use by the question.

## Example

Restrict communication between `etcd` and `api server` to the cipher `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` and also restrict the `api server` minimum TLS version to `TLS 1.2`

1. Edit the `API server` manifest and add the following two arguments

    ```
    --tls-min-version=VersionTLS12
    --tls-cipher-suites=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    ```

1. Edit the `etcd` manifest and add the following argument

    ```
    --cipher-suites=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    ```

1. Wait for both pods to restart. This may take a minute or more.

If for some reason one or both pods don't come back up, you should diagnose the cause [like this](https://github.com/kodekloudhub/community-faq/blob/main/docs/diagnose-crashed-apiserver.md).
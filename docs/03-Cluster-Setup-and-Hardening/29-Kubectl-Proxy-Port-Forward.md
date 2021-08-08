# Kubectl Proxy & Port Forward

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/kubectl-proxy-port-forward/)

In this section, we will take a look at `Kubectl Proxy` & `Port Forward`.

- Kubectl handles locating and authenticating to the apiserver. If you want to directly access the REST API with a http client like curl or wget, or a browser, there are several ways to locate and authenticate:

  - Run kubectl in proxy mode.
  - Uses stored apiserver location.
  - Verifies identity of apiserver using self-signed cert.


#### Using kubectl proxy
The following command runs kubectl in a mode where it acts as a reverse proxy. It handles locating the apiserver and authenticating.

    kubectl proxy --port=8080


#### Using kubectl port-forward
kubectl port-forward allows using resource name, such as a pod name, to select a matching pod to port forward to.

    kubectl port-forward service/nginx 28080:80

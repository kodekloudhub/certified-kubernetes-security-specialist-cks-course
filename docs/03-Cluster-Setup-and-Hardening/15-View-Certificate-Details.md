# View Certificate Details

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/view-certificate-details-2/)

In this section, we will take a look at `View Certificate Details`.

## View Certs
 ![hrd](../../images/hrd.PNG)

 ![hrd1](../../images/hrd1.PNG)

 - To view the details of the certificate.
   ```
   $ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
   ```

   ![hrd2](../../images/hrd2.PNG)

#### Follow the same procedure to identify information about all of the other certificates.

   ![hrd3](../../images/hrd3.PNG)

## Inspect Server Logs - Hardway setup
- Inspect server logs using `journalctl` command.
  ```
  $ journalctl -u etcd.service -l
  ```

  ![hrd4](../../images/hrd4.PNG)

## Inspect Server Logs - kubeadm setup
- View logs using `kubectl` tool.
  ```
  $ kubectl logs etcd-master
  ```
  ![hrd5](../../images/hrd5.PNG)

- View logs using `docker ps` and `docker logs` command.
  ```
  $ docker ps -a
  $ docker logs <container-id>
  ```
  ![hrd6](../../images/hrd6.PNG)

#### K8s Reference Docs

- https://kubernetes.io/docs/setup/best-practices/certificates/#certificate-paths
  

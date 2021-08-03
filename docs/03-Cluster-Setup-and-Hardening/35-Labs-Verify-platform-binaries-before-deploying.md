# Lab - Verify Platform Binaries before deploying

  - Take me to the [Lab](https://kodekloud.com/topic/labs-verify-platform-binaries/)

Solutions to Lab - Verify Platform Binaries before deploying.

- Run command

  <details>

  ```
  $ wget -O /opt/kubernetes.tar.gz https://dl.k8s.io/v1.20.0/kubernetes.tar.gz

  ```
  </details>

- Run command

  <details>

  ```
  $ shasum -a512 /opt/kubernetes.tar.gz
  
  ```
  </details>

- Run

  <details>

  ```
  
  $ cd /opt/
  
  $ tar -xf kubernetes.tar.gz
  
  $ cd kubernetes
  
  $ echo "v1.20.0-modified" > version
  
  $ cd ..
  
  $ tar -czf kubernetes-modified.tar.gz kubernetes
  
  $ shasum -a512 kubernetes-modified.tar.gz
      
  ```
  </details>

- Run command

  <details>
  
  ```
  $ shasum -a512 /opt/kubernetes.tar.gz

  ```
  </details>
  



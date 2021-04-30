# Labs Verify Platform Binaries before deploying
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704395)

Solutions to Labs Verify Platform Binaries before deploying
- Download kubernetes binary for version v1.20.0 and place it in the directory - /opt/

  <details>
  ```
  $ wget -O /opt/kubernetes.tar.gz https://dl.k8s.io/v1.20.0/kubernetes.tar.gz

  ```
  </details>
- What are first 10 characters of the shasum of previously downloaded file? Use sha512 hash mechanism

  <details>
  ```
  $ shasum -a512 /opt/kubernetes.tar.gz

  ```
  </details>
- Now lets see how shasum changes if we decompress and compress the same package with some modification


Decompress /opt/kubernetes.tar.gz in same folder and modify file named version and then compress the folder back with name /opt/kubernetes-modified.tar.gz.

Now compare shasum of new file with original

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
- What are first 10 characters of the shasum of previously downloaded file? Use sha512 hash mechanism

  <details>
  ```
  $ shasum -a512 /opt/kubernetes.tar.gz

  ```
  </details>
- To summarize: The shasum of a file changes when its contents are modified and should always be compared against the hash on the official pages to ensure the same file is downloaded.

  <details>
  ```
  OK

  ```
  </details>

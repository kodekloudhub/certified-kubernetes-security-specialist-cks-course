# Lab - Trivy
  
  - Take me to the [Lab](https://kodekloud.com/topic/labs-trivy/)

Solutions to Lab - Trivy:

1. 
  <details>

  ```
  apt-get  update
  apt-get install -y wget apt-transport-https gnupg lsb-release
  wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
  echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list

   #Update Repo and Install trivy
   apt-get update
   apt-get install trivy -y
   ```
   </details>


2.
  <details>

  ```
  trivy image <image-name>
  ```
  </details>

3.
  <details>
  
  ```
  Yes
  ```
  </details>

4.
  <details>

  ```
  Network
  ```
  </details>
  
5.
  <details>
  
  ```
  0.17.2
  ```
  </details>
  
6.
  <details>
  
  ```
  Ok
  ```
  </details>

7.
  <details>

  ```
  docker pull python:3.10.0a4-alpine

  trivy image --output /root/python_alpine.txt python:3.10.0a4-alpine
  ```
  </details>

8.
  <details>

  ``` 
  trivy image --severity HIGH --output /root/python.txt python:3.10.0a4-alpine
  ```
  </details>

9.
  <details>
  
  ```
  trivy image --input alpine.tar --format json --output /root/alpine.json
  ```
 </details>



 
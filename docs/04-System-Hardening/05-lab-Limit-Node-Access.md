# Lab - Limit Node Access
  
  - Take me to the [Lab](https://kodekloud.com/topic/lab-limit-node-access/)

Solutions Lab - Limit Node Access:

- **`0`** is the UID of superuser - root

- **`/etc/passwd`** is the file containing details about users in Linux.

- We have a user account named mail on the controlplane host. What kind of user account is it
  <details>

  ```
  System account
  ```
  </details>

- We have created a user named david on the controlplane host. What is the uid of this user?
  <details>

  ```
  Look at the file /etc/passwd and identify ID of david
  OR
  run as a root
  $ id david
  2323
  ```
  </details>

- Run
  <details>

  ```
  As a root run

  $ passwd david

  and then enter the password
  ```
  </details>

- Run
  <details>

  ```
  use those commands
  $ userdel ray
  $ groupdel devs
  and then enter the password
  ```
  </details>

- To suspend the user account, Run
  <details>

  ```
  $ usermod -s /usr/sbin/nologin himanshi
  ```
  </details>


- Run
  <details>

  ```
  $ useradd -d /opt/sam -s /bin/bash -G admin -u 2328 sam
  ```
  </details>



# Lab Limit Node Access
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704432

Solutions Lab Limit Node Access

- **`0`** is the UID of superuser - root

- **`/etc/passwd`** is the file containing details about users in Linux

- We have a user account named mail on controlplane host. What kind of user account is it
  <details>
  ```
  System account
  ```
  </details>

- We have created a user named david on controlplane host. What is the uid of this user?
  <details>
  ```
  Look at the file /etc/passwd and identify ID of david
  OR
  run as a root
  $ id david
  2323
  ```
  </details>

- Set a new password 874#@HDy1 for user david
  <details>
  ```
  As a root run
  $ passwd david
  and then enter the password
  ```
  </details>

- An unknown User called ray and group called devs was identified in the controlplane node. Delete this user and group from this host
  <details>
  ```
  use those commands
  $ userdel ray
  $ groupdel devs
  and then enter the password
  ```
  </details>

- There is a user named himanshi on the controlplane host. Suspend this user account so that this user cannot login to the system but make sure not to delete it.
  <details>
  ```
  Run
  $ uermod -s /usr/sbin/nologin himanshi
  ```
  </details>

- Create a user named sam on the controlplane host. The user's home directory must be /opt/sam. Login shell must be /bin/bash and uid must be 2328. Make sam a member of the admin group.
  <details>
  ```
  Run
  $ useradd -d /opt/sam -s /bin/bash -G admin -u 2328 sam
  ```
  </details>

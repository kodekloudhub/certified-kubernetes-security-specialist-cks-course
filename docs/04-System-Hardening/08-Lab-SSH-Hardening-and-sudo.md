# Lab SSH Hardening and sudo
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704435)

Solutions lab SSH Hardening and sudo

  - **`All of These`** are the authentication methods used by SSH

  - **`22`** is the default port used by SCP

  - **`ssh -i`** is used to pass in the private key while connecting to a server via SSH?

  - Create a user named jim on node01 host and configure password-less ssh access from controlplane host (from user root) to node01 host (to user jim).
  <details>
  ```
  $ ssh node01
  $ adduser jim
  set password you want
  Return back to controlplane host and copy ssh public key by running
  $ ssh-copy-id -i ~/.ssh/id_rsa.pub jim@node01
  test ssh access from controlpane host
  $ ssh jim@node01
  ```
  </details>

  - Change the password of user jim to 8DHdjjdk on host node01 and make him a sudo user.
  <details>
  ```
  $ ssh node01
  $ passwd jim
  switch to root user and run
  $ sudo su
  $ vi /etc/sudoers
  OR
  $ nano /etc/sudoers
  Add this line
  jim ALL=(ALL:ALL) ALL
  ```
  </details>

  - update user jim on node01 host so that jim can run sudo commands without entering the sudo password.
  <details>
  ```
  ssh into node01 by running
  $ ssh node01
  $ vi /etc/sudoers
  change this line from
  jim ALL=(ALL:ALL) ALL
  To
  jim ALL=(ALL) NOPASSWD:ALL
  ```
  </details>

  - Add a new user rob on node01 host and set password to jid345kjf. Make user rob member of the admin
  <details>
  ```
  SSH into node01 by running
  $ ssh node01
  Create user rob by running
  $ adduser rob
  Make it member of admin group by running
  $ usermod rob -G admin
  ```
  </details>

  - There is some issue with sudo on node01 host, as user rob is not able to run sudo commands, investigate and fix this issue.
  <details>
  ```
  $ ssh node01
  $ vi /etc/sudoers
  Edit this line to
  %admin ALL=(ALL) ALL
  ```
  </details>

  - Disable ssh root login and disable password authentication for ssh on node01 host.
  <details>
  ```
  $ ssh node01
  $ vi /etc/ssh/sshd_config
  Change: PermitRootLogin yes to no
  Change: #PasswordAuthentication yes to no
  Restart sshd service by running
  $ service sshd restart
  ```
  </details>

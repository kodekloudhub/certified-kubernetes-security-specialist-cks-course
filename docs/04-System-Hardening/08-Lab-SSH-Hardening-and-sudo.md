# Lab - SSH Hardening and sudo
  
  - Take me to [Lab](https://kodekloud.com/topic/lab-ssh-hardening-and-sudo/)

Solutions lab - SSH Hardening and sudo:

  - **`All of These`** are the authentication methods used by SSH

  - **`22`** is the default port used by SCP

  - **`ssh -i`** is used to pass in the private key while connecting to a server via SSH?

  - To create a user named jim on node01 host and configure password-less, Run
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

  - To change the password of user jim, Run
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

  - To investigate and fix this issue of sudo, Run
    <details>
    
    ```

    $ ssh node01
    $ vi /etc/sudoers
    Edit this line to
    %admin ALL=(ALL) ALL
    ```
    </details>

  - To disable ssh root login and disable password authentication, Run
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



# Lab - UFW Firewall Basics

  - Take me to the [Lab](https://kodekloud.com/topic/lab-ufw-firewall/)

Solutions to Lab - UFW Firewall Basics:

- To get the status of ufw tool on the nodes, Run
  <details>

  ```
  Find the status of the firewall by running the below command, if it is active that means that the tool is installed but needs to be enabled from the firewall

  $ ufw status
  ```
  </details>

- **`ufw status numbered`**  shows firewall status as a numbered list of RULES.

- **`ufw allow 1000:2000/tcp`**  used to allow a tcp port range between 1000 and 2000 in ufw.

- **`ufw reset`**  used to reset ufw rules to their default settings.

- On the node01 host, add a rule to allow incoming SSH connections.
Do not enable the firewall yet.
  <details>

  ```
  SSH into node01 by running
  $ ssh node01
  the allow the port by running
  $ ufw allow 22
  ```
  </details>

- We have some services on the node01 host, which are running on tcp port 9090 and 9091. Add ufw rules to allow incoming connection on these ports from IP range 135.22.65.0/24 to any interface on node01.
Once this is done, enable the firewall.
  <details>

  ```
  Run
  $ ssh node01
  $ ufw allow from 135.22.65.0/24 to any port 9090 proto tcp
  $ ufw allow from 135.22.65.0/24 to any port 9091 proto tcp
  ```
  </details>

- There is a Lighttpd service running on the node01. Identify which port it is bound to
  <details>

  ```
  First check the service and check if it is running
  lighttpd.service - Lighttpd Daemon
   Loaded: loaded (/lib/systemd/system/lighttpd.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2021-04-15 17:47:58 UTC; 46min ago
  Process: 6913 ExecStartPre=/usr/sbin/lighttpd -tt -f /etc/lighttpd/lighttpd.conf (code=exited, status=0/SUCCESS)
  Main PID: 6918 (lighttpd)
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/lighttpd.service
           └─6918 /usr/sbin/lighttpd -D -f /etc/lighttpd/lighttpd.conf

  Next, use netstat to find the port used by this process using
  $ netstat -natulp | grep lighttpd
  We can see that it is bound to port 80.
  ```
  </details>

- This service was identified to have several vulnerabilities in it. Disable the port 80 on node01 for ALL incoming requests.
  <details>

  ```
  Run
  $ ufw deny 80
  ```
  </details>

- We want to temporarily disable the ufw firewall on the node01 host but later we will enable it back so make sure to disable the firewall but preserve all rules so that the same can be effective when we enable it back.
  <details>

  ```
  Run
  $ ufw disable
  This will temporarily disable the firewall but the old rules are still maintained.

  ```
  </details>

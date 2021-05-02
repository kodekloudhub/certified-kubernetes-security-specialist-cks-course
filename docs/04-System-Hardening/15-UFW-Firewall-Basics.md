# UFW Firewall Basics
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704443)


In this section, we will take a look at UFW Firewall Basics

- UFW which stands for Uncomplicated Firewall. UFW is a simple frontend interface for Iptables, which as the name suggests provides a simple and easy to use interface to set up firewall rules.

- iptables is perhaps the most common tools used to configure the firewall

### UFW Rules

![fwRules](../../images/fwRules.png)

### Allowed & denied Rules
add an allow rule to permit SSH connections from the jump server with the IP 172.16.238.5

      ufw allow from 172.16.238.5 to any port 22 proto tcp


   ![allowedRules.png](../../images/allowed.png)


![delete.png](../../images/delete.png)

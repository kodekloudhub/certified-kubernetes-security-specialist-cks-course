# Lab - Identify and Disable Open Ports

  - Take me to the [Lab](https://kodekloud.com/topic/lab-identify-open-ports-remove-packages-services/)

Solutions to Lab - Identify and Disable Open Ports:

  - **`apt list --installed`**  is used to list all installed packages on an ubuntu system

  - Check if python2.7 package is installed on the controlplane host
    <details>
    
    ```
    # Run
    $ apt list --installed | grep python2.7
    You will find that is is not installed
    ```
    </details>

  - **`systemctl list-units --type service`**  can be used to list only active services on a system.

  - **`lsmod`**  can be used to list the kernel modules currently loaded on a system.

  - On controlplane host we have nginx service running which isn't needed on that system. Stop the nginx service and remove its service unit file. Make sure not to remove nginx package from the system
    <details>
    
    ```

    Run to get the unit name
    $ systemctl list-units --all | grep nginx
    Stop Nginx service by running
    $ systemctl stop nginx
    Find the location of the unit then remove it by running
    $ systemctl status nginx
    $ rm /lib/systemd/system/nginx.service
    ```
    </details>
  
  - We want to blacklist the evbug kernel module on controlplane host.
    <details>
  
    ```
  
    Edit in this file to blacklist a kernel module.
    $ vim /etc/modprobe.d/blacklist.conf
    Edit this line from #blacklist evbug to blacklist evbug
    ```
    </details>
  
  - Remove the nginx package from controlplane host.
    <details>
    
    ```
    # Run
    $ apt remove nginx -y
    ```
    </details>

  - We have a service running on controlplane host which is listening on port 9090. Identify the service and kill the same to free the 9090 port.
    <details>
    
    ```
    Identify the service listening on port 9090
    $ netstat -natp | grep 9090
    Kill/Stop the service to free the port
    $ systemctl stop apache2
    ```
    </details>
  
  - Check for updates available for wget package v1.18 and update to the latest version available in the apt repos.
    <details>
    
    ```
    # Run
    $ apt install wget -y
    ```
    </details>




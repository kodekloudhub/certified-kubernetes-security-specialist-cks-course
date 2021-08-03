#  Lab - Run-CIS-Benchmark-Assessment-tool-on-Ubuntu

  - Take me to the [Lab](https://kodekloud.com/topic/lab-run-cis-benchmark-assessment-tool-on-ubuntu/)

Solutions for lab Run-CIS-Benchmark-Assessment-tool-on-Ubuntu:-

- The full form of CIS is **`Center for internet Security`**.

- The **`Monitor global internet traffic`** is not a function of CIS.

- Run the test in interactive mode and use below settings: Benchmarks/Data-Stream Collections:
    `CIS Ubuntu Linux 18.04 LTS Benchmark v2.0.1`,<br>
    Profile : `Level 1 - Server`<br>
    Run following commands in the terminal:- <br>
    ```sh
      cd /root/Assessor-CLI<br>
      sh ./Assessor-CLI.sh -i -rd /var/www/html/ -nts -rp index      
    ```

- There are **`7`** tests failed for `1.1.1 Disable unused filesystems`.

- There are **`18`** tests passed for `2.2 Special Purpose Services`.

- The command **`nmcli radio all off`** should be used to fix the failed `test 3.6 Ensure wireless interfaces` are disabled.

- The command **`echo "Authorized uses only. All activity may be monitored and reported." > /etc/issue`** should be used to fix the failed `test 1.8.1.2 Ensure local login warning banner` is configured properly.

- The command **`apt install rsyslog`** should be used to fix the failed `test 4.2.1.1` and ensure rsyslog is installed.

- The command **`chown root:root /etc/crontab; chmod og-rwx /etc/crontab`** should be used to fix the failed `test 5.1.2` and ensure permissions on `/etc/crontab` are configured.





#  Lab - Run-CIS-Benchmark-Assessment-tool-on-Ubuntu

  - Take me to the [Lab](https://kodekloud.com/topic/lab-run-cis-benchmark-assessment-tool-on-ubuntu/)

Solutions for lab Run-CIS-Benchmark-Assessment-tool-on-Ubuntu:-

- The full form of CIS is **`Center for internet Security`**.

- The **`Monitor global internet traffic`** is not a function of CIS.

- Run the test in interactive mode and use below settings: <br>
    Benchmarks/Data-Stream Collections: `CIS Ubuntu Linux 20.04 LTS Benchmark v2.0.0`,<br>
    Profile : `Level 1 - Server`<br>
    Run following commands in the terminal:- <br>
    ```sh
      cd /root/Assessor 
      sh ./Assessor-CLI.sh -i -rd /var/www/html/ -nts -rp index      
    ```

    Here's a breakdown of the command for better understanding:

    - `sh`: This is the shell interpreter used to execute the script.
    - `./Assessor-CLI.sh`: This is the path to the CIS-CAT Pro Assessor command-line interface (CLI) script.
    - `-i`: This option typically indicates an "interactive" mode, where the tool may prompt the user for input or display interactive dialogs.
    - `-rd /var/www/html/`: This option specifies the root directory (/var/www/html/) to assess. This is where CIS-CAT Pro Assessor will look for configuration files to evaluate against the selected benchmark.
    - `-nts`: This option likely stands for "No Timestamps," and it might instruct the tool not to include timestamps in its output.
    - `-rp index`: This option allows you to specify a custom report prefix. When you use this option, the tool will generate a report with the specified prefix in the filename.<br>

- There are **`5`** tests failed for `1.1.1 Disable unused filesystems`.

- There are **`15`** tests passed for `2.2 Special Purpose Services`.

- What parameters should we set to fix the failed `test 4.2.7 Ensure SSH root login is disabled` ? `PermitRootLogin no`

- The command **`echo "Authorized use only. All activity may be monitored and reported." > /etc/issue`** should be used to fix the failed `test 1.7.2 Ensure local login warning banner` is configured properly.

- The command **`sudo apt update && sudo apt install -y rsyslog`** should be used to fix the failed `test 5.1.2.1` and ensure rsyslog is installed.

- The command **`chown root:root /etc/crontab; chmod og-rwx /etc/crontab`** should be used to fix the failed `test 4.1.2` and ensure permissions on `/etc/crontab` are configured.





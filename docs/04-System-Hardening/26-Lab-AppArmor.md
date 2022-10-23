# Lab - AppArmor

  - Take me to the [Lab](https://kodekloud.com/topic/lab-apparmor-2/)

Solutions Lab - AppArmor:

1. AppArmor is in **`beta`** state as of version 1.20.

1.  <details>
    <summary>Is the AppArmor module loaded in controlplane?</summary>

    ```
    aa-status
    ```

    </details>

1.  <details>
    <summary>How many AppArmor profiles have been loaded in total currently?</summary>

    From the output of `aa-status` in the previous question, answer is on the second line of output.

    </details>

1.  <details>
    <summary>How many AppArmor profiles have been loaded in enforce mode currently?</summary>

    From the output of `aa-status` in Q2, answer is on the third line of output.

    </details>

1.  <details>
    <summary>A pod called nginx has been deployed in the default namespace.</br>What is the status of this pod?</summary>


    ```
    kubectl get pods
    ```

    Check `STATUS` column.
    </details>

1.  <details>
    <summary>Why is this pod in a blocked state?</summary>

    Inspect the error message for this pod by running

    ```
    kubectl describe pod nginx
    ```
    </details>

1.  <details>
    <summary>What is the name of the AppArmor profile used by this pod?</summary>

    From the output of the command run for the previous question, get the profile name from the error message.
    </details>

1.  <details>
    <summary>Load the AppArmor profile called custom-nginx on controlplane and make sure that it is in enforced mode.</summary>

    The profile file is called `usr.sbin.nginx` located in the default AppArmor profiles directory.

    The default profiles directory is `/etc/apparmor.d`

    ```
    apparmor_parser -q /etc/apparmor.d/usr.sbin.nginx
    ```
    </details>

1. Information only.

1.  <details>
    <summary>This custom nginx pod serves static web pages at two urls... Which tabs are you able to access?</summary>

    Press the Site button. A browser tab will open on the root directory of the site. Duplicate the tab and  append `/allowed/` to one and `/restricted/` to the other.
    </details>

1. Information only.

1.  <details>
    <summary>Let's fix that. Another profile is created at <b>/etc/apparmor.d/usr.sbin.nginx-updated</b> which prevents reads on the restricted directory inside the container.</summary>

    Use this AppArmor profile and recreate this container.

    Make sure `restricted-nginx` is loaded by running

    ```
    aa-status
    ```

    If not loaded, then run

    ```
    apparmor_parser -q /etc/apparmor.d/usr.sbin.nginx-updated
    ```

    If you re-run `aa-status` you should now see the new profile `restricted-nginx` in the list of enforce mode profiles.

    The pod definition file is present at `/root/CKS/custom-nginx.yaml`

    Update the pod YAML file's annotation with the restricted-nginx apparmor profile and then recreate the pod.

    </details>

1. Retry the site.
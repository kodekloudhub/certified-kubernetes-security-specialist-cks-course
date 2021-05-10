# Labs Trivy
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31704758)

Solutions to Labs Trivy

  - Install the trivy vulnerability scanner on controlplane host.

      <details>

        ```
        apt-get  update
        apt-get install -y wget apt-transport-https gnupg lsb-release
        wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
        echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list

        #Update Repo and Install trivy
        apt-get update
        apt-get install trivy -y
        ```
      </details>


  - Which of the following commands can be used to scan container images using trivy?

    <details>

      ```
        trivy image <image-name>
      ```
    </details>


  - Can we scan tarball archives using trivy ?

      Answer: Yes

  - Which of the following artifacts cannot be scanned by trivy for security vulnerabilities?

    Answer: Network

  - Which version of trivy have you installed on the controlplane node?

    Answer: 0.17.2

  - Important Note: From version 0.8.0 and newer, trivy images are scanned using the trivy image subcommand.

  However, in older versions, to carry out a scan use the syntax: trivy image_name. In these versions, the image sub-command will not work.

  For example, notice the differences in scanning the nginx image:

    Version. 0.16: trivy image nginx

    Version: 0.8.0 and above: trivy nginx

  Answer: Ok


  - Pull python:3.10.0a4-alpine image on controlplane host and scan the same using trivy. Save the scan results in /root/python_alpine.txt file on controlplane host.

        docker pull python:3.10.0a4-alpine

        trivy image --output /root/python_alpine.txt python:3.10.0a4-alpine

  - We have a docker image python:3.6.12-alpine3.11 on controlplane host. Scan this image using trivy and filter out only high severity vulnerabilities in the report, finally save the report in /root/python.txt file on controlplane host itself.

        trivy image --severity HIGH --output /root/python.txt python:3.10.0a4-alpine


  - There is a docker image file /root/alpine.tar on controlplane host, scan this archive file using trivy and save the results in /root/alpine.json file in json format.

        trivy image --input alpine.tar --format json --output /root/alpine.json

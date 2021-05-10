# Use static analysis of user workloads
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704754)

In this section, we will take a look at Use static analysis of user workloads

- Kubesec helps analyze a given resource definition file and returns a score along with details about the critical issues that were found in it. In this case its rightly detected the issue with privileged container.


- To scan pod.yaml

      kubesec scan pod.yaml

  OR Use curl to POST a file to the server

      curl -sSX POST --data-binary @"pod.yaml" https://v2.kubesec.io/scan

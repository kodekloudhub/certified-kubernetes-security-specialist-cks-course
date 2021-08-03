# Use static analysis of user workloads

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/use-static-analysis-of-user-workloads-e-g-kubernetes-resources-docker-files/)

In this section, we will take a look at `Use static analysis of user workloads`.

- Kubesec helps analyze a given resource definition file and returns a score along with details about the critical issues that were found in it. In this case it's rightly detected the issue with privileged containers.

- To scan `pod.yaml` file.

      kubesec scan pod.yaml

  OR 
  
  Use `curl` command to `POST` a file to the server as follows:

      curl -sSX POST --data-binary @"pod.yaml" https://v2.kubesec.io/scan



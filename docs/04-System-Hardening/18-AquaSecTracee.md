# AquaSecTracee

  - Take me to [Video Tutorial](https://kodekloud.com/topic/aquasec-tracee/)

In this section, we will take a look at AquaSecTracee.

- Tracee is a Runtime Security and forensics tool for Linux. It is using Linux eBPF technology to trace your system and applications at runtime, and analyze collected events to detect suspicious behavioral patterns. It is delivered as a Docker image that monitors the OS and detects suspicious behavior based on a pre-defined set of behavioral patterns.

      docker run --name tracee --rm --privileged -v /lib/modules/:/lib/modules/:ro -v /usr/src:/usr/src:ro -v /tmp/tracee:/tmp/tracee -it aquasec/tracee:0.4.0 --trace comm=ls

    ![tracee](../../images/tracee.png)


### Reference

https://github.com/aquasecurity/tracee

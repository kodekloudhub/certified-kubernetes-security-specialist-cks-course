# Use Falco to Detect Threats

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/use-falco-to-detect-threats-2/)

In this section, we will take a look at `Use Falco to Detect Threats`.

  - Falco implements several rules by default - one of which alerted us that a shell was opened inside a  container and another one that warned us that sensitive files read inside the container.

  - A Falco rules file is a YAML.

### Detect Shell inside a container rule

rules.yaml

    - rule: Detect shell inside a container
      desc: Alert if a shell such as bash is open inside the container
      condition: container.id != host and proc.name = bash
      output: Bash opened (user=%user.name container=%container.id)
      priority: WARNING

 - The **`container.id`** and **`proc.name`** are known as **`Sysdig filters`** and they are extensively used by Falco. The Falco Policy engine makes use of these filters to extract information about an event such as the container ID, process name and several other.

    - Container.id filters the name of the container.

    - Proc.name–the name of the process.


 - In this previous we have used the value as **`WARNING`** but depending on the severity we can change them to any of –debug, informational, notice, warning,error, Critical alert,or emergency.

 ![falcoRules](../../images/falcoRules.png)




### References

- https://falco.org/docs/rules/supported-fields/

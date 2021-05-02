# Linux Syscalls
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704445)

In this section, we will take a look at Linux Syscalls

  - A kernel is the major component of an operating system and is the core interface between a computer’s hardware and its processes.

  - The Kernel can be divided in to two memory areas, user space and kernel space.
  ![kernel](../../images/kernel.png)



  - Applications running in user space get access to data on devices by making special requests to the kernel called system calls.

  - if we want to create an empty file called error.login the /tmp file system – it will make  several system calls.

    ![kernelTouch](../../images/kernelTouch.png)

  - Tracing the syscalls used by a process

  ![trace](../../images/trace.png)

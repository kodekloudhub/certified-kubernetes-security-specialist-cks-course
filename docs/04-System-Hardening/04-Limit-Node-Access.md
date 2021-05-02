# Limit Node Access
  - Take me to [Video Tutorial](https://kodekloud.com/courses/1378608/lectures/31704431)

In this section, we will take a look at Limit Node Access

 ### The Four types of user accounts in linux

  - The user account refers to individuals who need access to the Linux system.

  - A super user account is the root, which has UID 0.

  - System accounts are typically created during the OS installation.

  - Service accounts are similar to system accounts and  created when services are installed in linux. Such as an nginxservice that makes use of a service account called nginx.

###  Access control files in Linux

- Most of the access control files in Linux are stored under the /etc directory.  

- The first one of interest is the /etc/passwd file which is also commonly known as the password file. This file contains basic information about the users in the system, including the username,UID/GID, home directory and their default shell.

- The passwords are stored in the next file we are going to see the /etc/shadow file. The contents of this file is hashed.

- The /etc/group file stores information about all user groups on the system such as the group name and GID and members.

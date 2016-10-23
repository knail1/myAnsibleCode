# Setting up jenkins container on the ubuntu VM

### Prologue:
- I intend to replace these manual steps by using ansible-container, specifically the yaml files made by Damon Parker located [here](https://github.com/damonp/ansible-container-jenkins)
- right now, for the sake of expediency, i'm going to record only the manual steps I ran here to get the jenkins setup working on the ubuntu VM...

- pull the latest container from docker hub:

`gubuntu1 [jenkinsUbuntuVM]$ sudo docker pull jenkins`
```
b654f6b529a1: Pull complete
662249d63ca0: Pull complete
Digest: sha256:d48b1c77a66c5f4c6be42d0cccea73ec75f1306ef23d5f220cb8477682da43c1
Status: Downloaded newer image for jenkins:latest

gubuntu1 [jenkinsUbuntuVM]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
jenkins             latest              9bc67dd3e379        37 hours ago        712 MB
```
- running as a daemon, but with a local directory mapped to /var/jenkins_home to ensure installed plugins/settings are not lost at reboot/restart

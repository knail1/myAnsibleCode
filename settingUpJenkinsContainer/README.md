# Setting up jenkins container on the ubuntu VM

### Prologue:
- I intend to replace these manual steps by using ansible-container, specifically the yaml files made by Damon Parker located [here](https://github.com/damonp/ansible-container-jenkins)
- right now, for the sake of expediency, i'm going to record only the manual steps I ran here to get the jenkins setup working on the ubuntu VM...

- pull the latest container from docker hub:

`gubuntu1 [jenkinsUbuntuVM]$ sudo docker pull jenkins`
`[sudo] password for knail1:
Using default tag: latest
latest: Pulling from library/jenkins

43c265008fae: Downloading [=======>                                           ] 7.339 MB/51.35 MB
af36d2c7a148: Downloading [===========>                                       ] 4.325 MB/18.53 MB
143e9d501644: Downloading [=====>                                             ] 4.698 MB/42.5 MB
bfc4cdbc8d81: Waiting
7bbb0894483a: Waiting
343785c6e48e: Waiting....
`
- running as a daemon, but with a local directory mapped to /var/jenkins_home to ensure installed plugins/settings are not lost at reboot/restart

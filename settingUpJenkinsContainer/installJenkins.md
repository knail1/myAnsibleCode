# Setting up jenkins container on the ubuntu VM

### Prologue:
- I intend to replace these manual steps by using ansible-container, specifically the yaml files made by Damon Parker located [here](https://github.com/damonp/ansible-container-jenkins)
- right now, for the sake of expediency, i'm going to record only the manual steps I ran here to get the jenkins setup working on the ubuntu VM...

- pull the latest container from docker hub:

```
gubuntu1 [jenkinsUbuntuVM]$ sudo docker pull jenkins`
b654f6b529a1: Pull complete
662249d63ca0: Pull complete
Digest: sha256:d48b1c77a66c5f4c6be42d0cccea73ec75f1306ef23d5f220cb8477682da43c1
Status: Downloaded newer image for jenkins:latest
```
```
gubuntu1 [jenkinsUbuntuVM]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
jenkins             latest              9bc67dd3e379        37 hours ago        712 MB
```
- now, let's run as a daemon, but with a local directory mapped to /var/jenkins_home to ensure installed plugins/settings are not lost at reboot/restart
	* jenkins container is running as jenkins:jenkins (1000:1000) and needs access to the local directory using the same uid:gid
	* my id: knail1, incidentially has the same uid:gid (1000:1000) with which I created the local_jenkins_dir , so I didnt really have to do antying special to give jenkins user in container access to the hosts directory (they had the same uid:gid!)

```

gubuntu1 [jenkinsUbuntuVM]$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
...
knail1:x:1000:1000:Omer Amsari,,,:/home/knail1:/bin/ksh93 <-------- note uid:gid
 
gubuntu1 [jenkinsUbuntuVM]$ ls -la
total 0
drwxr-xr-x 1 knail1 knail1 102 Oct 23 15:27 .
drwxr-xr-x 1 knail1 knail1 238 Oct 23 15:15 ..
drwxr-xr-x 1 knail1 knail1  68 Oct 23 15:27 local_jenkins_home
gubuntu1 [jenkinsUbuntuVM]$ echo $PWD
/home/knail1/mnt/dockerImages/jenkinsUbuntuVM
```

- so let's fire her up!!!! :
```
gubuntu1 [jenkinsUbuntuVM]$ sudo docker run --name myJenkins -d -p 8080:8080 -p 50000:50000 -v $PWD/local_jenkins_home:/var/jenkins_home jenkins
2dc861d43c722e23d4816a30b627230658053cab120cc9575313fea1fc9eb21f
gubuntu1 [jenkinsUbuntuVM]$

```

- check if she's running:
```
gubuntu1 [jenkinsUbuntuVM]$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
2dc861d43c72        jenkins             "/bin/tini -- /usr/lo"   17 seconds ago      Up 16 seconds       0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   myJenkins

```

- good! lets get the one-time password to log into the GUI from logs..
```
gubuntu1 [jenkinsUbuntuVM]$ sudo docker logs 2dc861d43c72
Running from: /usr/share/jenkins/jenkins.war
webroot: EnvVars.masterEnvVars.get("JENKINS_HOME")
Oct 23, 2016 8:33:17 PM org.eclipse.jetty.util.log.JavaUtilLog info
...

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

9ffa539b499849b898be99b1cc052a8d

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

....
```

- now, I can log into the jenkins container from my Mac using http://gubuntu1:8080 , and provide the one-time password to log in


personal note: user: admin, password: 2nd standard for jenkins


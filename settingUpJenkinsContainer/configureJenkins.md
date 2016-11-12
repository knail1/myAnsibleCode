# Configuring Jenkins

## Prologue:
- here we will dive into : various initial configuration stuff, SMTP, first job, plugins etc

### cleaning up tmp junk:
- through GUI I set up a job to:
	- cleaning /tmp/ directory every "H 23 * * *" for leftover build crap . Used the following to avoid job from failing (since rm -r * in /tmp would only remove stuff owned by jenkins and invariably bomb on stuff owned by root)
	- I used the following shell script in this job to clean the fils:

```
cd /tmp
rm -r `ls -la | grep 'jenkins' | awk ' {print $9}'`
```


logged into the jenkins running container via:
```
ubuntu1 [knail1]$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
2dc861d43c72        jenkins             "/bin/tini -- /usr/lo"   2 weeks ago         Up 4 minutes        0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   myJenkins

gubuntu1 [knail1]$ sudo docker exec -it myJenkins bash
jenkins@2dc861d43c72:/$
```

	on the container, under /var/jenkins_home/jobs/CleaningTempDirectory/
		config.xml has the actual job config..

```
<?xml version='1.0' encoding='UTF-8'?>
<project>
  <actions/>
  <description></description>
  <keepDependencies>false</keepDependencies>
  <properties/>
  <scm class="hudson.scm.NullSCM"/>
  <canRoam>true</canRoam>
  <disabled>false</disabled>
  <blockBuildWhenDownstreamBuilding>false</blockBuildWhenDownstreamBuilding>
  <blockBuildWhenUpstreamBuilding>false</blockBuildWhenUpstreamBuilding>
  <triggers>
    <hudson.triggers.TimerTrigger>
      <spec>H 23 * * * </spec>
    </hudson.triggers.TimerTrigger>
  </triggers>
  <concurrentBuild>false</concurrentBuild>
  <builders>
    <hudson.tasks.Shell>
      <command>cd /tmp
rm -r `ls -la | grep &apos;jenkins&apos; | awk &apos; { print $9 }&apos;`</command>
    </hudson.tasks.Shell>
```

- also set up SMTP with a gmail account to send out emails, and tested against my emails (worked fine)
- also installed periodic plugin through the GUI and set up the weekly backup under /var/jenkins_home/backups (which maps to a local directory on my box) *need to do this via CLI as well*


## misc. section: setup to access jenkins via CLI:

- create a new user (e.g. api user)
- for that user put the public ssh key of your local user on the mac (that is : ~/.ssh/id_rsa.pub)
- in manage jenkins> global security , ensure security is enabled with a fix port (note : i had 50000 on my container though not sure how fixed vs random makes a difference since the CLI access is through the web port 8080..)
- do stuff like:

```
knail1s-MBP.home [jenkinsUbuntuVM]$ java -jar jenkins-cli.jar -s http://gubuntu1:8080/ help

(shows all the CLI commands possible)
knail1s-MBP.home [jenkinsUbuntuVM]$ java -jar jenkins-cli.jar -s http://gubuntu1:8080/ who-am-i
Enter passphrase for /Users/knail1/.ssh/id_rsa:
Authenticated as: apiUser
Authorities:
  authenticated
knail1s-MBP.home [jenkinsUbuntuVM]$
```

###TODO:
re-do all the GUI commands in the CLI format for more verbosity.


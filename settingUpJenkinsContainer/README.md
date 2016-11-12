# Installing, configuring, experimenting with Jenkins

## Summary:
I have set this folder up for documenting , from scratch, some tips and tricks on installing and playing around with Jenkins in order for fast ramp-up for anyone interested.. this does expect the user to have stepped through earlier set up of local VM on virtualbox, installing docker, setting up environments etc.. look at the other folders for more details..

## Sections:
- [Install Jenkins](https://github.com/knail1/myAnsibleCode/blob/master/settingUpJenkinsContainer/installJenkins.md) *setting up jenkins on the local Ubuntu VM*
- [Configure](https://github.com/knail1/myAnsibleCode/blob/master/settingUpJenkinsContainer/configureJenkins.md) *going through various initial configuration stuff, SMTP, first job, plugins etc*
- [Continuous Integration - basic](https://github.com/knail1/myAnsibleCode/blob/master/settingUpJenkinsContainer/CI-basic.md) *branching set up, integrating with git (poll, build, unit test, merge)*
- Install Artifactory :  *setting up jenkins on the local Ubuntu VM*
- Continuous Integration - advanced  : *code quality checked (sonarQube), introducing integration testing,branch, pushing to artifactory..*
- Continuous Delivery: *setting up testing server, deploying shippable code to Artifactory.*
- Continuous Deployment:  *integration to production branch to production server (targets: current:tomcat etc, med term future: container build and deploy into cloud foundry, final: microservices build and deploy into cloud foundry, updates to APIGee*


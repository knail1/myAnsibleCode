# myAnsibleCode

I keep all my ansible code for local or cloud dev environments here
- look at settingUpUbuntuVM.txt as manual steps to get VM ready for ansible
- while the steps are written for a local ubuntu VM on virtual box, they can easily translate for a VM in AWS, etc 


## settingUbuntuEnv/ubuntuEnvPlaybook.yml                  

- this playbook
	- installs ksh (my favorite shell, throwback from my days at ATT)
	- changes the provided username's shell to ksh
	- updates that username's .profile with relevant settings (making vi as CLI editor, and setting prompt)
- run this script as follows:
$ /usr/local/bin/ansible-playbook -vvvv -u knail1 --ask-sudo-pass -e "username=knail1" settingUbuntuEnv/ubuntuEnvPlaybook.yml 

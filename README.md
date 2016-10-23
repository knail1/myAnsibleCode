# myAnsibleCode

I keep all my ansible code for local or cloud dev environments here
- look at settingUpUbuntuVM.txt as manual steps to get VM ready for ansible
- while the steps are written for a local ubuntu VM on virtual box, they can easily translate for a VM in AWS, etc 


## settingUbuntuEnv/ubuntuEnvPlaybook.yml                  

- this playbook
	- installs ksh (my favorite shell, throwback from my days at ATT)
	- changes the provided username's shell to ksh
	- updates that username's .profile with relevant settings (making vi as CLI editor, and setting prompt)
	- 10.23: installs vim and sets up .vimrc
- run this script as follows:

`$ /usr/local/bin/ansible-playbook -vvvv -u knail1 --ask-sudo-pass -e "username=knail1" settingUbuntuEnv/ubuntuEnvPlaybook.yml `

## settingUpDocker/dockerUp.yml

- this playbook installs docker on the ubuntu VM
- pre-requisites for this playbook: install the role:

`sudo ansible-galaxy install angstwad.docker_ubuntu`

- then run this command:
` $ ansible-playbook -vvvv -u knail1 --ask-sudo-pass settingUpDocker/dockerUp.yml`


## settingUpDevEnvironmentOnVM/

- this is not really a playbook..
- El-Capitan SUCKS! doesn't let me write to certain "protected" parts of file-system. whatever
- i moved all my ansible development to the ubuntu VM itself. 
	* included mounting the Mac's ansibleCode directory into ubuntu VM,
	* setting up VM user's public key into github
	* blah blah
	* instructions on those can be found here: settingUpDevEnvironmentOnVM/settingUpAnsibleOnUbuntuVM.txt



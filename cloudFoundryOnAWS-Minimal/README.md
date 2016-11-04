# this file documents an install of cloud foundry (MINIMAL version) on AWS. I didn't want to follow the normal install (which is 20+ VMs)

*Original instructions of this install are at:*
*https://github.com/cloudfoundry/cf-release/tree/master/example_manifests*

*My goal is to automate this complete install at some point. this file will be updated via version control as I automate various sections...*

## The steps are:

1. create a dir ~/my-bosh
2. create manifest within that dir: bosh.yml (available [here](https://bosh.io/docs/init-aws.html#create-manifest))

3. set up AWS account and put required params in bosh.yml (detailed instructions with snapshots here: https://bosh.io/docs/init-aws.html#prepare-a)
	* [create](https://bosh.io/docs/aws-iam-users.html#create) a new IAM user and replace ACCESS-KEY-ID and SECRET-ACCESS-KEY in bosh.yml
	* create a VPC : https://bosh.io/docs/init-aws.html#create-vpc
	* create Elastic IP, keypair, and configure security group: https://bosh.io/docs/init-aws.html#create-eip

		
4. deploy bosh director [link](https://bosh.io/docs/init-aws.html#deploy)
	* first, set up bosh-init and other *nix packages
			* install bosh-init (to create the bosh director VM) : https://bosh.io/docs/install-bosh-init.html
		*remember, since stupid el-capitan doesn't let me do any pip installs for aws cli, i'm running bosh from my ubuntu VM. therefore I need to install bosh-init and other garbage there*

			* I created an ansible script for this (the script also includes fixes for a bug with bosh deploy)
			*note: this yml we can run from our mac laptop as it is targeting the [ubuntu] host which is my gubuntu1 VM on virtualbox*

			```
			$ ansible-playbook -vvvv -u knail1 --ask-sudo-pass -e "username=knail1" installing-required-apt-shit.yml
			```

		* deploy the manifest: 
			```
			gubuntu1 [my-bosh]$ bosh-init deploy ./bosh.yml
			```

		* note: i created an aws-cfu user in IAM on AWS, and the instructions on bosh.io stated aws-cpi. shouldn't matter as bosh uses the user key and secret key to log in...

### errors:
during install it compiled ruby_aws_cpi, etc etc fine, but then croaked here:
    CPI 'create_stemcell' method responded with error: CmdError{"type":"Bosh::Clouds::CloudError","message":"Unable to create a connection to AWS; please check your region or EC2 endpoint.\nIaaS Error: #\u003cSocketError: getaddrinfo: Name or service not known\u003e","ok_to_retry":false}


after troubleshooting I found a bug with bosh, and filed it along with hacky workaround:
https://github.com/cloudfoundry/bosh/issues/1496

(essentially aws cli doesn't work well with iam especially since it creates the URL as: iam.<region>.amazonaws.com for its API calls, but IAM is one of the few services which is not region specific, and therefore is instantiated at, and only works against, iam.amazonaws.com. I hacked up my ubuntu VM's /etc/hosts to make it work!)


this ran for a while and finally finished.. some sample output:

```

Started installing CPI
  Compiling package 'ruby_aws_cpi/5e8696452d4676dd97010e91475e86b23b7e2042'... Finished (00:00:00)
  Compiling package 'bosh_aws_cpi/c2563067cbc20e3406ea36ca2984df2f0eaebcb0'... Finished (00:00:00)
  Installing packages... Finished (00:00:00)
  Rendering job templates... Finished (00:00:00)
  Installing job 'aws_cpi'... Finished (00:00:00)
Finished installing CPI (00:00:01)

Starting registry... Finished (00:00:00)
Uploading stemcell 'bosh-aws-xen-hvm-ubuntu-trusty-go_agent/3263.10'... Skipped [Stemcell already uploaded] (00:00:00)

Started deploying
  Creating VM for instance 'bosh/0' from stemcell 'ami-51c1e546 light'... Finished (00:00:32)
  Waiting for the agent on VM 'i-0455578e1aa1d29a4' to be ready... Finished (00:01:05)
  Creating disk... Finished (00:00:12)
  Attaching disk 'vol-0b131b05cec57b9fc' to VM 'i-0455578e1aa1d29a4'... Finished (00:00:14)
  Rendering job templates... Finished (00:00:02)
  Compiling package 'ruby/589d4b05b422ac6c92ee7094fc2a402db1f2d731'... Finished (00:02:40)
  Compiling package 'mysql/b7e73acc0bfe05f1c6cbfd97bf92d39b0d3155d5'... Finished (00:00:31)
  Compiling package 'libpq/09c8f60b87c9bd41b37b0f62159c9d77163f52b8'... Finished (00:00:21)
  Compiling package 'ruby_aws_cpi/5e8696452d4676dd97010e91475e86b23b7e2042'... Finished (00:02:19)
  Compiling package 's3cli/f0852d518b2950c875a67a7183e9805d3d6906dd'... Finished (00:00:02)
  Compiling package 'davcli/5f08f8d5ab3addd0e11171f739f072b107b30b8c'... Finished (00:00:14)
  Compiling package 'registry/caf2488fe5d35e698c790618c526036f553daa31'... Finished (00:01:25)
  Compiling package 'health_monitor/f930e11825f04cd5dbbbf1a5c78906648d600fa7'... Finished (00:01:12)
  Compiling package 'genisoimage/008d332ba1471bccf9d9aeb64c258fdd4bf76201'... Finished (00:00:19)
  Compiling package 'nginx/21e909d27fa69b3b2be036cdf5b8b293c6800158'... Finished (00:00:45)
  Compiling package 'postgres/4b9f6514001f7c3f7d4394920d6aced9435a3bbd'... Finished (00:03:04)
  Compiling package 'nats/0155cf6be0305c9f98ba2e9e2503cd72da7c05c3'... Finished (00:00:15)
  Compiling package 'bosh_aws_cpi/c2563067cbc20e3406ea36ca2984df2f0eaebcb0'... Finished (00:00:58)
  Compiling package 'director/11ea11e0ae4fdcd91432b0de7191faf5e63ba12f'... Finished (00:01:33)
  Updating instance 'bosh/0'... Finished (00:00:17)
  Waiting for instance 'bosh/0' to be running... Finished (00:00:27)
  Running the post-start scripts 'bosh/0'... Finished (00:00:00)
Finished deploying (00:18:37)

Stopping registry... Finished (00:00:00)
Cleaning up rendered CPI jobs... Finished (00:00:00)
gubuntu1 [my-bosh]$
```

5. the installed the bosh cli on the ubuntu VM (Forget the crappy el-capitan )
[link](https://bosh.io/docs/bosh-cli.html)
	* added a few more pre-requisite apt-get packages to the earlier ansible file
	* then ran the gem command on the VM..
	```
	sudo gem install bosh_cli --no-ri --no-rdoc
	```

	also added some more ansible to easily access bosh director via the elastic IP (added into ~/.profile via the shit yml file)
	`export ELASTIC_IP=52.206.184.215`


	* test the CLI:
	```
	gubuntu1 [my-bosh]$ bosh target $ELASTIC_IP
	Target set to 'my-bosh'
	Your username: admin
	Enter password:
	Logged in as 'admin'
	gubuntu1 [my-bosh]$
	gubuntu1 [my-bosh]$ bosh vms
	Acting as user 'admin' on 'my-bosh'
	No deployments
	```

6. Update the BOSH Security Group to allow "cf logs" to work, back to the minimal aws install git hub page:
[link](https://github.com/cloudfoundry/cf-release/tree/master/example_manifests)

	* git clone https://github.com/cloudfoundry/cf-release
	* move the example-manifests/minimal-aws.yml to the my-bosh/ directory and hack it per instructions

	I'll upload the original minimal-aws.yml to github, but not the modified version, since it would have a private key

7. Create a NAT machine in the bosh subnet
	stopping here, 1:30am, need to sleep... https://github.com/cloudfoundry/cf-release/tree/master/example_manifests#create-a-nat-machine-in-the-bosh-subnet



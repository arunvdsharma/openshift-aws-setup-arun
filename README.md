# Openshift 3.11 installation on AWS Cloud
This repository explains the steps of installing Openshift 3.11 with AWS Cloud configurations.

- Use inventory.ini file to start the installation.

Common steps taken on all the nodes:
=======================================
sudo vi /etc/ssh/sshd_config
    PermitRootLogin yes    	#Uncomment this line in /etc/ssh/sshd_config and save it.
sudo systemctl restart sshd

By default IAM doesn't allow to login with root user on EC2 instances but you can use the authroized_keys of centos user to access it.

sudo -s
cp /home/centos/.ssh/authorized_keys /root/.ssh/authorized_keys

yum install -y git
git clone https://github.com/arunvdsharma/openshift-centos.git
cd openshift-centos

chmod +x install-tools.sh
./install-tools.sh

Exit from the nodes but master.

============================================
Installation of Openshift AWS on Master node
============================================
chmod +x install-openshift.sh

#### Ensure you have the right inventory.ini file with AWS configurations in it. Check if that file has below properties
	openshift_cloudprovider_aws_access_key=AWS_ACCESS_KEY
	openshift_cloudprovider_aws_secret_key=AWS_SECRET_KEY
	openshift_clusterid=openshift
	openshift_cloudprovider_kind=aws

#### If you want to configuration S3 to store the volumes, add below properties
	openshift_hosted_manage_registry=true
	openshift_hosted_registry_storage_kind=object
	openshift_hosted_registry_storage_provider=s3
	openshift_hosted_registry_storage_s3_accesskey=AWS_ACCESS_KEY
	openshift_hosted_registry_storage_s3_secretkey=AWS_SECRET_KEY
	openshift_hosted_registry_storage_s3_bucket=openshift-bucket-aruns
	openshift_hosted_registry_storage_s3_region=ap-south-1a
	openshift_hosted_registry_storage_s3_chunksize=26214400
	openshift_hosted_registry_storage_s3_rootdirectory=/registry
	openshift_hosted_registry_pullthrough=true
	openshift_hosted_registry_acceptschema2=true
	openshift_hosted_registry_enforcequota=true
	openshift_hosted_registry_replicas=3

Note:
	Every master host, node host, and subnet must have the tags defined as below format 
	     e.g. kubernetes.io/cluster/<clusterid>,Value=(owned|shared)
	One security group, preferably the one linked to the nodes, must have the 
	     kubernetes.io/cluster/<clusterid>,Value=(owned|shared) tag


./install-openshift.sh

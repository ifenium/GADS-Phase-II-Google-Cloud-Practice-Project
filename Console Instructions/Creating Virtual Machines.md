
# LAB:  Creating Virtual Machines

## Objectives:

In this lab, you explore the available options for VMs and see the differences between locations.

In this lab, you learn how to perform the following tasks:

-   Create several standard VMs
    
-   Create advanced VMs
##  Task 1: Create a utility virtual machine

### Steps:

1. Create a Compute Engine with the following specifications:
	- **Name:**   Type a name for your VM
 	- **Region:** us-central1
	- **Zone:** us-central-c
	- **Machine type:** n1-standard-1 (1 vCPUs, 3.75 GB memory)
	- **External IP:** None
	- **Boot-Disk-Size:** 10 GB
	- **Boot disk type:** SSD persistent disk
	- **Image:** Debian GNU/Linux 10 (buster)
	```
	# Replace <PROJECT_ID> and <INSTANCE_NAME> with your current Project ID and selected Instance Name.
	
	gcloud beta compute --project=<PROJECT_ID> instances create <INSTANCE_NAME> --zone=us-central1-c --machine-type=n1-standard-1 --no-address --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=<INSTANCE_NAME>       
	```
3. Explore the VM details
	```
	gcloud compute instances describe <INSTANCE_NAME> --zone='us-central1-c'
	```
4. Explore VM Logs
	
	**Note that Compute Engine Activity logs are to be deprecated on Sep 30, 2020 and will be replaced with Audit logs.** 	
	```
	# Legacy activity log 
	
	gcloud logging read jsonPayload.resource.name=<INSTANCE_NAME>
	```
	```
	# Audit log
	
	gcloud logging read protoPayload.resourceName:<INSTANCE_NAME>
	```
## Task 2: Create a Windows virtual machine

### Steps:
1. Create a Compute Engine with the following specifications:
    - **Name:**   Type a name for your VM
	- **Region:** europe-west2
	- **Zone:** europe-west2-a
	- **Machine type:** n1-standard-2(2 vCPUs, 7.5 GB memory)
	- **External IP:** None
	- **Boot disk size:** 100 GB standard persistent disk
	- **Boot disk type:** SSD persistent disk
	- **Image:** Windows Server 2016 Datacenter Core
	- **Firewall:** Allow HTTP traffic and Allow HTTPS traffic
	Ensure you replace **<PROJECT_ID>** with your corresponding Project ID in the sample codes below.
	```
	# Creating the Windows Virtual Machine
	
	gcloud beta compute --project=<PROJECT_ID> instances create mywindowsife --zone=europe-west2-a --machine-type=n1-standard-2 --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=ethereal-anthem-286107 --tags=http-server,https-server 
	```
	```
	# Allow HTTP traffic
	
	gcloud compute --project=<PROJECT_ID> firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
	```
		# Allow HTTPS traffic
		
		gcloud compute --project=<PROJECT_ID> firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server
	```
2. Set the password for the VM
	
	Use the [`gcloud compute reset-windows-password`](https://cloud.google.com/sdk/gcloud/reference/compute/reset-windows-password) command to create a new account and password or reset the existing account password for the logged in user:

	```
	gcloud compute reset-windows-password <INSTANCE_NAME>
	```
	You will be presented with a confirmation prompt; this will need to be accepted by entering **Y** and/or pressing **Enter**. It can be rejected by entering **N**, then pressing **Enter**.
	
	```
	This command creates an account and sets an initial password for the
	user [username] if the account does not already exist.
	If the account already exists, resetting the password can cause the
	LOSS OF ENCRYPTED DATA secured with the current password, including
	files and stored passwords.

	For more information, see:
	https://cloud.google.com/compute/docs/operating-systems/windows#reset

	Would you like to set or reset the password for [username] (Y/n)?
	```
	Once confirmed, confirmation of password reset will look as follows.

	```
	Resetting and retrieving password for [username] on [instance-name]
	Updated [https://www.googleapis.com/compute/v1/projects/project-name/zones/zone/instances/instance-name].
	ip_address: ip-address
	password:   password
	username:   username
	```
	
## Task 3: Create a custom virtual machine

### Steps:
1. Create a Compute Engine with the following specifications:
    - **Name:**   Type a name for your VM
	- **Region:** us-west1
	- **Zone:** us-west1b
	- **Machine type:** Custom
	- **Cores:** 6 vCPU
	- **Memory:** 32GB
	Ensure you replace **<PROJECT_ID>** with your corresponding Project ID in the sample codes below.

	```
	gcloud beta compute --project=<PROJECT_ID> instances create <INSTANCE_NAME> --zone=us-west1-b --machine-type=e2-custom-6-32768 --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=<INSTANCE_NAME> 
	```
2.  Connect via SSH to your custom VM

	1. Replace the following:
	-   `PROJECT_ID`: the ID of the project that contains the instance
	-   `ZONE`: the name of the zone in which the instance is located
	-   `VM_NAME`: the name of the instance
		```
		gcloud compute ssh --project <PROJECT_ID> --zone <ZONE> <VM_NAME>
		```
	2. To see information about unused and used memory and swap space on your custom VM, run the following command:
		```
		free
		```
	3. To see details about the RAM installed on your VM, run the following command:
		```
		sudo dmidecode -t 17
		```
	4. To verify the number of processors, run the following command:
		```
		nproc
		```
	5. To see details about the CPUs installed on your VM, run the following command:
		```
		lscpu
		```
	6. To exit the SSH terminal, run the following command:
		```
		exit 
		```
		
3. Exit the console
	```
	exit
	```

## Task 4: Review
In this lab, 
-  You created several virtual machine instances of different types with different characteristics. One was a small utility VM for administration purposes. 
-  You also created a standard VM and a custom VM. 
-  You launched both Windows and Linux VMs and deleted VMs.

## Congratulations!

You have completed the Creating Virtual Machines lab.

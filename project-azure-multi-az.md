```
# Creating RG: rg-web ( resource group )

# Creating VNET: vnet-web ( virtual network )

# Creating VM: vm-web ( virtual machine )
	Ubuntu 20.04 LST Gen 2 
	B2s 
	azure-ssh-key 
	SSH (22) and HTTP (80)
	Disk: Standard SSD

  From Advanced | Custom Data and Cloud Init ...insert the script:
		
#!/bin/bash
apt update
apt install -y apache2
systemctl start apache2
systemctl enable apache2
cd /var/www/html
wget https://objectstorage.us-ashburn-1.oraclecloud.com/p/1MmaFBFpuhMnxUAnS4eUdP9QA-tZ_Pg0pLJBEq5hq4eNOvdzBrC9-gV5huHXfjSa/n/idqfa2z2mift/b/eventos-thecloudbootcamp/o/DESAFIO_OCI_2021/webserverFiles.zip
apt install -y unzip
unzip -o webserverFiles.zip
apt install -y stress

	► Testing Stress tool
	
	► Testing VM Public IP
	

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓	

# Creating RG: rg-webserver

# Creating Custom Image from the VM created (vm-web): image-webserver
	Go to vm-web
	Click on "Capture"
		>> No, capture only a managed image.
		
	...Stopping virtual machine!!
	
	Around 3 ~ 4 minutes to finish!
	
	► After create the Image...Delete the vm-created "vm-webserver"

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓	

# Creating VMSS: vmss-webserver ( Virtual Machine Scale Set )
	Zones 1,2,3 
	Image | See all Images - My Images: image-webserver
	▓ Creating a New VNET for VMSS: vnet-vmss | subnet-vmss
	▓ Network Interface: nic-vmss | Inbound Ports: SSH (22) and HTTP (80) 
	
	▓ Scaling: 
		Initial instance count: 0 
		Scaling policy: Custom 
		Min: 1 
		Max: 3 
		CPU % 70 
		5 min (minimum allowed) - 1 
		
	▓ Advanced (Custom Data):
	
#!/bin/bash
cd /var/www/html
sed -i '34i '"$(hostname -I)"'' index.html
		
Testar a Vm criada!

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓	

# Creating LB: lb-webserver ( load balancer )
	Frontend IP: front-webserver
	Create Pub IP: front-ip 
	Backend Pool: backend-pool-webserver | vmss-webserver
	Inbound Rules: Add a LB Rule => lb-rule-webserver
    Health Probe: Create a New => hp-webserver | HTTP

# Testing LB Frontend IP - DO NOT WORK!!!

	► Go to the VMSS and check the Instances "Latest Model", If it has "No", then, open the instance and Upgrade it!

# Test again the LB Frontend IP! =)

# Stress test
	
	stress -c 2 -t 1200

6 a 7 minutos

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓	

Deleting Resources: 

	► Just delete de RG! That's All! :)
	

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓	

References:
	
Error "Latest model" "No" 
https://stackoverflow.com/questions/70668793/azure-vmss-instance-latest-model-meaning
https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model

--------------------------------------------------------------
You can start, stop your VM's using Azure powershell commands.

Start-azurermvm -resourcegroupname "rg-webserver" -name "vm-webserver"

Stope-azurermvm -resourcegroupname "rg-webserver" -name "vm-webserver"
```
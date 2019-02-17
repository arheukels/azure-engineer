#My experience and further notes

Put here general information about the steps you took, what you liked, disliked, why did you do X instead of Y and so on.

## Azure Storage Account

Requirements  storage account:
 
What is the purpose of the storage account?
I need to know this because when table storage is needed, then Blob storage is not usable (Blobs, Table, Queues, File).
 
What are the expected IOPS needed?
I need to know the required performance, for example a DB server require more IOPS.
Default storage is standard (HDD) or Premium (SSD), the premium storage is faster, it is only more expensive.
I use the Microsoft Azure pricing calculator to compare the price difference.
 
What is the recommended name for the storage account?
Maybe there is a naming convention document available that can be used.
The storage account name needs to be unique and lowercase. 
 
What is the recommended name for the Resource Group?
Maybe there is a naming convention document available that can be used.
 
What is the Location where the storage account needs to be created?
I need to know the required location, for example "West Europe"
 
Is there a requirement for redundancy?
Storage accounts make always use of replication and default, it is (RA-GRS) "Read-access geo-redundant storage"
If redundancy is not that important, then choose for LRS. One node may be unavailable whitin a Microsoft data centre.
 
Who needs to have access?
Who needs to have access and the permission level (RBAC).
 
Are there more Storage Accounts needed with the same configuration?
If so, I can make an ARM-template or PowerShell script to create the storage accounts.
 
 
Q2. Secure storage account:
All storage accounts are encrypted by default and use 256-bit AES.
It is possible to use/create your own encryption keys and put them in KeyVault.
 
Limit access to storage account keys to reduce key exposure.
Use SAS keys for customers that need a specific time range and permissions to access and not the "Access Keys" (Blob, File, Queue, Table)
 
All data is secure transferred by default using HTTPS.
It is possible to give only access to a specific IP-range or Virtual Network for better security.
 
When creating a Blob container, use public Private access (no anonymous access) for better security.
In private mode you need to have the SAS access key to access the Blob container, for example by using Microsoft Azure Storage Explorer.
 
Show .net developer easy way to check if data is written:
 
One of the easiest ways to check if data is written, is by using "Microsoft Azure Storage Explorer" available for Windows, Linux, MacOS.
It is also possible to use PowerShell or Az CLI.
 
Storage Accounts have the following URL based on
Blob: http://<Storage-Account-Name>.blob.core.windows.net
Table: http://<Storage-Account-Name>.table.core.windows.net
Queue: http://<Storage-Account-Name>.queue.core.windows.net
File: http://<Storage-Account-Name>.file.core.windows.net
 
 
## VNet-to-VNet

Ask for their ip plan.
Peering between VNET's with overlapping IP's will not work.
 
Which ports need to be accessible?
This is needed to assign an ‘allow inbound rule’ on both NSG's of Vnet1 and Vnet2 connect to the NIC’s.
Check if there is a separate NSG connected to the subnet if so create an inbound rule and allow the port.
 
Is there a Firewall active on both of the VM's?
If so, create an ‘allow inbound rule’ on the OS firewall for the port.
 
Test communication between both VM's
Use Azure "Network Watcher" to easily test communications between both VM's
Select "Connection trouble-shooter" select VM1 (Source) and VM2 (Destination)
Of course it is also possible to connect to one of the VM's and do a ping.
 
I created a simple ARM-template and Power Shell script to create two Vnets and peering between them.
I really like to learn more about Terraform or Ansible, for now I am not familiar with it.
 
The ARM-Template can be deployed from Visual Studio or by using PowerShell "New-AzureRmResourceGroupDeployment -Name VnettoVNet -ResourceGroupName "RG-VnettoVnet" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -verbose"
 
The Power Shell script can be deployed with for example ".\Create-TwoVnetsandPeering.ps1 -RSG RG-test -VnetName1 Vnet1 -VnetAddPrfx1 "10.1.0.0/16" -VnetSubname1 Subnet1 -SubAddPrfx1 "10.1.0.0/24" -VnetName2 Vnet2 -VnetAddPrfx2 "10.2.0.0/16" -VnetSubname2 Subnet2 -SubAddPrfx2 "10.2.0.0/24""


### Application Gateway

Application GateWay is a traffic load balancer that can be used to route traffic between different tenants or URL's.
It is useful for companies that want to route traffic to different backend pools for example if it want to route specific traffic to tenant A or B. 
It is also useful if you want to separate traffic like video or web traffic to different backend pools, for example if you have a pool of web servers and pool of video servers and you want to separate the traffic.
 
I am not familiar with Terraform and it will take too much time to study it and cannot finish it on time, sorry for that. I could create a Power Shell or AZ CLI script instead.
 
Steps:
1.	Create Resource Group
2.	Create Vnet with subnets
3.	Create the Application GateWay
4.	Create VM's for pool 1 & 2 with IIS
5.	Create backend pools
6.	Create backend listner
7.	Create path based rule and define path for pool 1 and 2

# Azure Bastion Host. 

Managed RDP/SSH to VMs over SSL using private IP on the VM
Azure Bastion is a fully managed PaaS service that provides secure and seamless RDP and SSH access to your virtual machines directly through the Azure Portal. Azure Bastion is provisioned directly in your Virtual Network (VNet) and supports all VMs in your Virtual Network (VNet) using SSL without any exposure through public IP addresses.

We will be creating Bastion Hosts using the following ARM template. 

This ARM template will create following resources. 

1. Bastion Hosts
2. Virtual Network VNET
3. Two subnet
   I  AzureBastionSubnet - Default and Madndatory subnet for Bastion hosts with /27 prefix to CIDR. 
   II AdditionalSubnet - Additional Subnet we are creation to create a Virutal machine in it. 

Prerequiste
-----------
First create a Resource Group. Please use the below command to create a resource group using Azure CLI command. 

Update the location and resource group name before executing the command. All the below commands can be executed from Azure Cloud Shell (Bash).

<img src=cloudshell.PNG>

```
az group create -l <Location> -n <ResourceGroupName>
```
## Create Azure credentials
Packer authenticates with Azure using a service principal. An Azure service principal is a security identity that you can use with apps, services, and automation tools like Packer. You control and define the permissions as to what operations the service principal can perform in Azure.

Create a service principal with az ad sp create-for-rbac and output the credentials that Packer needs:

Create an Image using packer. It will create an image with Ubuntu Linux, Nginx and MySQL database. 

```
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

An example of the output from the preceding commands is as follows:

```
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Please update the below JSON file to create and image. Kindly update the following in this JSON file

```
"client_id": "",
"client_secret": "",
"tenant_id": "",
"subscription_id": "",
"managed_image_resource_group_name": "",
"managed_image_name": "",
```
Here is the JSON available. Download the JSON file and update required details and store it on your local drive. 
https://github.com/sabil05/IaC/blob/master/Ubuntu_MySQL

Once you are done with updating JSON file. Upload the file into the Azure cli and execute the following command. 

<img src=uploadfile.PNG>

```
packer build <JSON file name>
```
<img src=packer.PNG>

Once the image creation has been complete. Kindly make a note of the Image name, which you have proivde as an input to the another JSON file. 

Now deploy the another JSON file which will create an Bastion Host, VNET, SUBNET, VM and LB. Once you are able to deploy these service sucessfully. 

You will be able to access VM using bastion host. 


Cick on below to deploy the deployment. Please input all the required filed to complete the deployment. 

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fsabil05%2FIaC%2Fmaster%2FARM_application.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


Once you are done with successful deployment. Please go to the <b> Virtual Machine -> Select VM which you want to connect through Bastion host -> Click Connect -> choose bastion.</b>

Please provide the Login crednatials and then you will be able to access it. 

You can validate the Load balancer as well. <b> Go to Load Balancer -> Select the load balacer -> Copy the public IP and enter in address of a broweser.</b> 

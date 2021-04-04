# Virtual Machines

Azure reserves the first four addresses and the last address in each subnet for its use.  

Services Required to Create a VM in Azure:  
- vm
- Storage account to hold VHD
- VHD
- virtual network
- network interface to connect to vnet
- public id (optional)

Billing is based on CPU compute time and storage capacity.  

VMs should have two VHDs by default. One for the OS and one as temporary storage. Data on the temp  storage disk will be lost if the VM is rebooted so store application data on a third VHD.  

OS Disk : `/dev/sda` for linux, `C:/` for windows, maximum capacity of 2048gb   
Temporary Storage: `/dev/sdb` mounted at `/mnt` for linux, `D:/` for windows. Stores the swap file.  
Each additional disk can store up to 32 gibibytes of data.  

Extra disks must be formatted and mounted to the vm on the first startup.  
Initialize the disk: `(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc`  
Format the disk with a file system: `sudo mkfs -t ext4 /dev/sdc1`  
Mount the disk at `data`: `sudo mkdir /data && sudo mount /dev/sdc1 /data`  

*1 Gibibyte = 1.074 Gigabyte*  

Only 64-bit OS are supported.  

Create a ssh key for Azure Linux VM:  
`ssh-keygen -m PEM -t rsa -b 4096`  

Install the public ssh key onto an azure VM:  
`ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver`  

*The default network settings for new VM block all incoming traffic except for traffic on the same vnet and allow all outbound traffic.*  


*Scaling Disks*  
A single storage account supports up to 40 vhd. More storage accounts must be created if the limit is reached.  
Unmanaged -  manual scaling.  
Managed 
- automatically creates new storage accounts as needed.  
- VHDs are spread across Azure infrastructure for higher reliability
- supports RBAC 
- Snapshot support
- Backup support. Can be automatically backed up to a different region.

*Network Security Groups*  
Software-based firewalls used within Azure virtual networks.  
- control traffic flow on subnets
- control inbound/outbound traffic on individual vms

For inbound traffic, Azure processes the security group associated to the subnet and then the security group applied to the network interface. Outbound traffic is handled in the opposite order (the network interface first, followed by the subnet).  

All Security Groups include a "Deny-All" clause.  

Rules are evaluated from lowest to highest priority.  


*Availability Sets*  
Groups of identical VMs. Each VM in an availability set is in a different Fault Domain and Update Domain so that if a rack in a data center goes down or undergoes an upgrade any apps running on the VMs will not be interrupted.  
VMs deployed within an availablity set of at least two machines are guaranteed 99.5%  availability.  
Fault Domain - logical group of components that share a single point of failure, such as a server rack in a AZ datacenter  
Update Domain - logical group of hardware that is updated/ rebooted simultaneously  

Scale Up/Down - increasing/decreasing the CPU/RAM size of the VM(s)  
Scale Out/In - increase/decrease the number of VMs  


### Dedicated Hosts
Physical Servers can be purchased from Microsoft. This give customers full control over the server. No other customers will be able to run services on the server. Multiple private servers are called a `host group`.

### Azure Automation Services
- Process Automation
    - set up watcher tasks that monitor vms and run scripts when an event occurs
- Configuration Management
    - Automate system configuration changes or updates of vms
- Update Management
    - Automatically installs updates and patches


### Azure Site Recover
Deploys VMs to secondary site if primary deployment site fails


# App Service   
Billing is based on the resources used by each Web App ( CPU, RAM) and bandwidth consumed.  

App Service Plan Settings
- OS
- CPU/RAM
- SLA
- Automatic backup/restore

*Services will be interrupted when scaling Up/Down*  

### Deployment Slots
Each instance of the web app has a different hostnames. When  slots are swapped the hostnames are switched and traffic to the instances is immediately changed. Settings for the slot( database connection strings, etc) are also swapped.  

All of the slots in an App Service plan shares the CPU and RAM of that service plan.  

Using a staging environment to compile a web app can reduce "cold start" delays before deploying to production. App Service will also make a request to the root of the app when a swap is performed.  

Slot settings can be copied from other slots but application code must be deployed from git or docker.  

When apps slots are swapped the settings of the target slot are applied to the source swap before hostnames are changed.  

*Slot Swapping Preview*  
Slot-Swapping Preview can be used to check if everything works correctly before swapping hostnames.

Phase 1  
Settings from target slot are applied to source. Swap operation pauses so source slot can be tested with target slot settings.  
Phase 2  
Host names are swapped.


*Auto Swap*  
Automatically swap slots for CI/CD. Not available in App Service on Linux  




### Deploy a web app to App Service with Azure CLI  
Get resource names  
```
APPNAME=$(az webapp list --query [0].name --output tsv)
APPRG=$(az webapp list --query [0].resourceGroup --output tsv)
APPPLAN=$(az appservice plan list --query [0].name --output tsv)
APPSKU=$(az appservice plan list --query [0].sku.name --output tsv)
APPLOCATION=$(az appservice plan list --query [0].location --output tsv)
```

Deploy:  
```
az webapp up --name $APPNAME --resource-group $APPRG --plan $APPPLAN --sku $APPSKU --location "$APPLOCATION"
```

# Azure Container Registry
Used to store Docker images on Azure

Advantages over DockerHub:
- full control over who can see and use images
- images can be digitally signed to avoid corruption or infection
- all images are encrypted at rest
- choice of data center
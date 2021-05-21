# Virtual Machines

Azure reserves the first four addresses and the last address in each subnet for its use.  

Services Required to Create a VM in Azure:  
- vm
- Storage account to hold VHD
- VHD
- virtual network
- network interface to connect to vnet
    - public ip
    - private ip
- public id (optional)

Billing is based on CPU compute time and storage capacity.  
*Pay as you Go*  
pay for compute capacity by the second
*Reserved Instances*  
Purchae a vm for 1-3 years in a specific region at a discount.  

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
*Fault Domain* - logical group of components that share a single point of failure, such as a server rack in a AZ datacenter  
*Update Domain* - logical group of hardware that is updated/ rebooted simultaneously  

*Autoscaling*
- available in Standard plan and higher
Cool Down Period - After a scale up/scale down event has been triggered, the amount of time to allocate/deallocate machines  
Scale Up/Down - increasing/decreasing the CPU/RAM size of the VM(s)  
Scale Out/In - increase/decrease the number of VMs  

### Connecting
If you select "Create New SSH Pair" when creating a new vm you can download the .pem private key.  
- set permissions to `400`
- connect to vm with: ` ssh -i my_key.pem  azureuser@xxx.xxx.xxx.xxx`

### AZ Backup Service
Can be used to create automatic, scheduled backups of vm disks. Installs an extension on the vm which takes snapshots of the attached disk(s).  
Three types of snapshots:  
- Application - captures memory content and pending I/O operations
- File System - snapshot of file system
- Crash - created when vm crashes

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

### Azure Backup Service
- automatically allocates and manages backup storage space
- charged for storage space used, not charged for data transfer
- Locally redundant
    - backups are stored in same region
- geo-redundant
    - backups are stored in other regions
- data is encrypted in transit and at rest
- no time limit


# App Service   
Deploys web applications onto vms which are managed by the service. Different plans are offered. Each one has a pre-defined vm. Billing is based on the selected plan, resources used by each Web App ( CPU, RAM), and bandwidth consumed. The advantage of using this service over VM service is that you don't have to configure or manage the vms.  

The plan settings define the vm:
- OS
- CPU/RAM
- SLA
- Automatic backup/restore

When an app is created it is put into an App Service Plan and then run on all vms in the ASP. if multiple apps are in the APS they share the same vms. All deployment slots in the APS also share the vms.


*Pricing Tiers*  
- Shared Compute: Free and Shared plans
    - apps run on the same vms as other customers apps. Cannot scale out.
    - dynamic ip addresses
- Dedicated Compute - Basic , Standard, Premium/V2 plans
    - apps run on dedicated vms
    - supports scaling out
    - app in the same Service Plan share same vms
    - dynamic ip addresses
- Isolated - App Service Environments 
    - dedicated VMs
    - dedicated Vnets
    - static ip addresses

Apps that are not in Isolated tier share network infractructure with other apps. All ASPs and apps that are not in the isolated tier share the same set of IP addresses. These IP addresses are part of Azure's infracturcture. In the Isolated tier ip addresses are assigned to the ASP itself.  

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


### App Service Environments
ASE is used for high-volume applications that will need to be able to horizontally scale in large amounts. These apps also typically have high memory usage and RPS( Requests Per Second).  

*Workers* - VM that hosts the customer's app  
*Front End* - Handle http/s requests and load balance to workers.  
Both Front Ends and Workers are added automatically by azure as the service scales in/out.  

- Workers are available in three sizes:
    - 2 cpu/ 4gb ram
    - 4 cpu/ 16gb ram
    - 8 cpu/ 32gb ram
- All instances exist within the customer's Vnet, allowing fine-grained network control.
- static, dedicated ip addresses for inbound and outbound connections
- Supports connecting to on-site resources over VPN
- up to 100 instances



# Azure Container Registry
Used to store Docker images on Azure

Advantages over DockerHub:
- full control over who can see and use images
- images can be digitally signed to avoid corruption or infection
- all images are encrypted at rest
- choice of data center

# Azure Container Instances
- Deploy isolated containers without managing underlying container manager
- Persist data using Azure File Shares
- Container gets public IP and FQDN

# Azure Kubernetes Service
Kubernetes is a Container Orchestration Platform. Kubernetes is installed on each vm in a group of vms known as a `cluster`. One of the vms is designated as the `main` node. This node is used to manage the other nodes.
Pods- a logical grouping of containers within a node

Benefits of kubernetes:  
- load balancing
- healing - If a pod fails the main node will create a new one
- rolling deployments - if an image is updated the main node will created new containers from the updated image one container at a time so services are not disrupted.
- can replicate pods



# ARM Templates
- Resources
    - what will be deployed
- Variables
    - values to use in template
- Parameters
    - values used in deployment phase
- Outputs
    - values returned from deployed resources

# Application Gateway
Application Gateway is a traffic load balancer.  
- When used with Azure Kubernetes it runs in a pod and balances traffic to other pods
- Url based routing. Requests for `/foo` can  be routed to the Foo server, requests for `/bar` are routed to the Bar server.
- Multiple-site hosting. Multiple domain names are supported on a single instance. Both domain names will point to the same IP address but the AG routes requests internally to the appropriate service.
    - subdomains can also be hosted on the same AG deployment.
- Redirection
    - Http/ https 
    - external sites
    - change port
    - change path
- session affinity - cookie based sessions are always routed back to the same server
- Can rewrite http headers and urls to hide sensitive internal network information
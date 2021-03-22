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


### Azure Automation Services
- Process Automation
    - set up watcher tasks that monitor vms and run scripts when an event occurs
- Configuration Management
    - Automate system configuration changes or updates of vms
- Update Management
    - Automatically installs updates and patches


### Azure Site Recover
Deploys VMs to secondary site if primary deployment site fails



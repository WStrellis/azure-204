# Virtual Machines

Azure reserves the first four addresses and the last address in each subnet for its use.  

1. Design network
2. Assign subnets
3. Determine datacenter
4. Set CPU, RAM, Storage

Billing is based on CPU compute time and storage capacity.  

VMs should have at least two VHDs. One for the OS and one as temporary storage. Data on the temp  storage disk will be lost if the VM is rebooted so store application data on a third VHD.  

Only 64-bit OS are supported.  

*Scaling Disks*  
A single storage account supports up to 40 vhd. More storage accounts must be created if the limit is reached.  
Unmanaged -  manual scaling.  
Managed -automatically creates new storage accounts as needed.  

*Network Security Groups*  
Software-based firewalls used within Azure virtual networks.  
- control traffic flow on subnets
- control inbound/outbound traffic on individual vms

*Availability Sets*  
Groups of identical VMs. Each VM in an availability set is in a different Fault Domain and Update Domain so that if a rack in a data center goes down or undergoes an upgrade any apps running on the VMs will not be interrupted.  
VMs deployed within an availablity set of at least two machines are guaranteed 99.5%  availability.  
Fault Domain - logical group of components that share a single point of failure, such as a server rack in a AZ datacenter  
Update Domain - logical group of hardware that is updated/ rebooted simultaneously  

BUBB4burg3rBowser

### Azure Automation Services
- Process Automation
    - set up watcher tasks that monitor vms and run scripts when an event occurs
- Configuration Management
    - Automate system configuration changes or updates of vms
- Update Management
    - Automatically installs updates and patches


### Azure Site Recover
Deploys VMs to secondary site if primary deployment site fails
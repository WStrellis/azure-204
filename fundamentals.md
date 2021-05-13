# Regions
An Azure Region is a distinct set of data centers. Regions are assigned a partner region that is geographically close by to  form "Region Pairs". Data and services should be deployed to paired regions to minimize recovery time. If both regions are down Microsoft selects one as the "priority" region and fixes that one first. If data is not backed up to both regions in a pair it could be placed into a region that is not the priority in the event of an outage. 

# Availability Zones
Physically separate datacenters within a region. Provide redundancy in case a datacenter is down.

# Networking
Azure reserves the first four and last addresses in each VNet for its own use.  
# Azure Security Center
- Limit vm access based on a time schedule or ACL
- Monitors system processes on VMs. Uses ML to identify malware that has been installed.
- Monitors network traffic and makes suggestions for NSG(network security group ) settings.
- Performs file integrity checks
- Works for both cloud and on-prem resources

# Advanced Data Security
Enables data discovery and classification of MS SQL Server data by assigning data labels and columns.  
- Automatically scans data and make suggestions for potentially sensitive data
- Tagged columns represent meta data which is read by the SQL engine. Meta data can be used for security purposes.
- Make queries on classification meta data
- View classification data in AZ dashboard

# Azure Key Vault
Stores sensitive information in an encrypted state. Supports RBAC for key access.  

Access is divided into "Data Plane" and "Management Plane". This allows apps to read from the data plane but not change anything, and for granting users the ability to read vault properties and tags but not the data within a vault.  

If a user has `Contributor Role` Permissions they can grant themselves access to the data plane by setting a key vault access policy.  
`Key Vault Contributor` role can access management  features but not data plane.  

Two services:  
- Hardware Protected
    - Uses HSM(hardware security module) to generate and perform cryptographic operations on keys
    - recommended for production environments
- Software Protected
    - Uses software to generate and perform cryptographic operations on keys
    - less expenisve
    - recommended for dev/test environments

SSL certficates can be stored in Key Vault and automatically generated/updated:  
Manual Update process:  
- Your application creates a key in your Azure Key Vault.
- Key Vault returns a Certificate Signing Request (CSR) to your application.
- Your application passes the CSR to your chosen CA.
- Your chosen CA responds with an X.509 Certificate.
- Your application completes the new certificate creation with a merger of the X.509 Certificate from your CA.
Automatic Update process:  
- Your application creates a certificate by creating a key in your key vault.
- Key Vault sends a SSL Certificate Request to the CA.
- Your application polls, in a loop and wait process, for your Key Vault for certificate completion. The certificate creation is complete when Key Vault receives the CA’s response with x509 certificate.
- The CA responds to Key Vault's SSL Certificate Request with an X509 SSL Certificate.
- Your new certificate creation completes with the merger of the X509 Certificate for the CA.


# SQL Information Protection ( SQL IP)
- AZ SQL Auditing tracks db events and writes to a log  Storage Account or Log Analytics Event Hub
- Uses ADS data classification
- Masks data for non-priveleged users
- Encrypts db data, backups, and logs at rest

# Data Retention and Legal Holds
AZ Blob Storage supports assigning Data Retention Policies and Legal Holds to data. The data becomes immutable. It cannot be modified or deleted until the Data Retention Policy expires or the Legal Hold is removed.

- applied to a storage container
- a container can have both a legal hold and data retention policy applied
- legal hold takes precedence over expired data retention policy

# Regions
An Azure Region is a distinct set of data centers. Regions are assigned a partner region that is geographically close by to  form "Region Pairs". Data and services should be deployed to paired regions to minimize recovery time. If both regions are down Microsoft selects one as the "priority" region and fixes that one first. If data is not backed up to both regions in a pair it could be placed into a region that is not the priority in the event of an outage. 

# RBAC

Access is granted to users, groups , and applications at a scope level.  
Inheritance order: Management Group -> Subscription -> Resource Group -> Resource  

RBAC Components:  
*Security Principal*  
the user, group, or application that will be granted access  

*Role Definition*  
A set of rules defining the permissions of a Role  
Built-In Roles:  
- Owner - Has full access to all resources, including the right to delegate access to others.
- Contributor - Can create and manage all types of Azure resources, but can’t grant access to others.
- Reader - Can view existing Azure resources.
- User Access Administrator - Lets you manage user access to Azure resources.

*Scope*  
Where the access applies to. Access granted at Parent scope( ex: resource group) applies to Child scope( ex: vm)  

Permissions can be set using a combination of Actions and Not-Actions. `actions` are what the role grants permission to do. `not-actions` are things the role does not allow. Set to `actions: *` and then use not-actions do remove things the role should not have.


## Azure Storage Overview
Four types of data:  
- Blob : used for large amounts of text or binary data
- Files
- Queues
- Table Storage: NoSQL

A single Azure subscription can host up to 200 storage accounts, each of which can hold 500 TB of data.  

A single storage account has a fixed I/O limit of 20,000 operations per sec. This can support but to 40 standard VHDs.  

Data is automatically encrypted using a 256-bit AES cipher before being stored, and decrypted when read.  

Virtual Hard Disks(VHD) can be encrypted. Bitlocker for Windows, dm-crypt for Linux.  

*Azure Defender For Storage*  
Triggers an alert when an anomaly occurs. Sends alerts via email.  

Azure Data Lake Storage Gen 2 supports ACLs and OAuth2 bearer tokens.

## DB Security
AZ SQL Database has built-in firewall protections.
- Default settings block all public access
- uses TLS for all connections
- Data at rest with Transparent Data Encryption(TDE). Data is decrypted when read into memory and encrypted when written to disk. db data, log files, and backups are encrypted.
- Supports Data Masking. the data of some columns can be substituted with asterisks or other strings depending on the user accessing the data. DB admins can always see unmasked data.
- Supports auditing. Off by default.

the  firewall  can be configured with Server-level rules and Database-level rules  
Server-Level  
- filter by azure services
- filter by ip address
- filter by vnet
Database Level
- filter by ip

Auditing  can be configured at the Server or DB level. DB level should only be used if the auditing configuration of one db on a server is different from the other dbs.  

### Advanced Data Security for  SQL DB
- data discovery and classification
- vulnerability assessment
- advanced threat protection - automated ML threat hunting

### DB Security Best Practices:
- put services that access a db into a vnet. use Server-level db firewall rules to grant access to the vnet.
- Create users on the db for each application that will access the db. Only grant access the tables the application needs.




## Azure Blob Storage
Azure Blog storage is used to store large amounts of unstructured data.  

Default permissions require authentication to access a blob.  Individual containers can be configured to allow public access to all of the blob data within them.  

Good things for blob storage:  
- storing/serving files
- streaming video/audio
- logging data

Three types of blobs:  
Block: used for storing text or binary data. Each block is read from beginning to end.  
Append: Designed for fast append operations, such as logging. No update/delete capability.   
Page: data is stored in pages. used for frequent reads and writes. Random-access, can read/write any part of page. Used for Azure VM virtual HDs.  

## Authorization
Access Keys - Allow full access to a storage account  
Shared Access Signature( SAS ) - Provided per-user/group access rights
- Service-Level : allow access to specific resources in storage account
- Account-Level : service level plus additional resources and abilities



CLI Commands  
`storage account create` - Create a general-purpose V2 Storage account.  
`storage account key list` - Retrieve the storage account key.  
`storage account show-connection-string` - Retrieve the connection string for an Azure Storage account.  
`storage container create` - Creates a new container in a storage account.  
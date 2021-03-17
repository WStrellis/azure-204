## Azure Storage Overview
Four types of data:  
- Blob : used for large amounts of text or binary data
- Files
- Queues
- Table Storage: NoSQL

A single Azure subscription can host up to 200 storage accounts, each of which can hold 500 TB of data.  

Data is automatically encrypted using a 256-bit AES cipher before being stored, and decrypted when read.  

Virtual Hard Disks(VHD) can be encrypted. Bitlocker for Windows, dm-crypt for Linux.  

*Azure Defender For Storage*  
Triggers an alert when an anomaly occurs. Sends alerts via email.  

Azure Data Lake Storage Gen 2 supports ACLs and OAuth2 bearer tokens.

## Azure Blob Storage
Azure Blog storage is used to store large amounts of unstructured data.
Good things for blob storage:  
- storing/serving files
- streaming video/audio
- logging data

Three types of blobs:  
Block: used for storing text or binary data. Each block is read from beginning to end.  
Append: Designed for fast append operations, such as logging.   
Page: data is stored in pages. used for frequent reads and writes. Can read/write any part of page.  

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
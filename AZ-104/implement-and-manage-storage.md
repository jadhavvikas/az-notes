# Implement and manage storage

## What is Azure Storage?
Stores data by:
- database - Azure SQL Database, Azure CosmosDB, Azure Table Storage
- messages - Azure Queues and Azure Event Hubs
- loose files - Azure Files and Azure Blobs
Azure Storage are made up of four services: Azure Blobs, Azure Files, Azure Queues, and Azure Tables.

### What is a storage account
It’s a container that groups a set of Azure Storage services together - combining data services in a storage account lets you manage them as a group. Storage account settings apply to all the data services in the account.
(subscription, location, performance, replication, access tier, secure transfer required)
- name, deployment model, account kind apply to the account itself
    - name - name of storage account
    - deployment model - the system Azure uses to organize your resources; it defines  the api you use to create configure, and manage those resources.
        - resource manager (current model - Azure Resource Manager API) (recommended)
        - classic (legacy model - Azure Service Management API)
    - account kind - a set of policies that determine which data service you can handle in the account and the pricing of those offers
        - StorageV2 (general purpose v2) (current kind, supports all storage types and features)
        - Storage (general purpose v1) (legacy kind, supports all storage types but not all features)
        - BlobStorage (legacy that allows only block blobs and append blobs)
* General-purpose v2 accounts: Basic storage account types. Recommended for most scenarios
* General-purpose v1 accounts: Legacy account type (use v2 instead)
* BlockBlobStorage accounts: Storage accounts with premium performance characteristics. Recommended for scenarios with high transactions rates, or for smaller objects
* FileStorage accounts: Files-only storage accounts with premium performance characteristics
* BlobStorage accounts: Legacy blob-only storage accounts. Use v2 instead when possible
Storage account type	Supported services	Redundancy options	Deployment model
General-purpose V2	Blob, File, Queue, Table, Disk, and Data Lake Gen22	LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS3	Resource Manager
General-purpose V1	Blob, File, Queue, Table, and Disk 	LRS, GRS, RA-GRS	Resource Manager, Classic
BlockBlobStorage	Blobs (block blobs and append blobs only)	LRS, ZRS3	Resource Manager
FileStorage	File only	LRS, ZRS3	Resource Manager
BlobStorage	Blob (block blobs and append blobs only)	LRS, GRS, RA-GRS	Resource Manager

### Azure storage access tiers:
* Hot - Optimized for storing data that is accessed frequently.
* Cool - Optimized for storing data that is infrequently accessed and stored for at least 30 days.
* Archive - Optimized for storing data that is rarely accessed and stored for at least 180 days with flexible latency requirements, on the order of hours.

## Disks storage
You can choose whether to let Azure manage the storage infrastructure for your virtual hard disks (VHDs) or manage that yourself.

### Disk Roles
Each disk can take one of three roles in a virtual machine (* means VMs get these disks by default):
* Operating System disk (/dev/sda). one disk in each VM that contains OS files
* Data disk. one or more data virtual disks to each virtual machine to store data
* Temporary disk (dev/sdb). a single temp disk used for short-term storage applications (page/swap files)

### Ephemeral OS disks
A virtual disk that saves data on the local virtual machine storage. It’s has faster read-and-write latency than a managed disk, also faster to reset the image to the original boot state. Works well when you want to host a stateless workload (i.e. business logic for multi-tier website or micro-service).
Managed disks
Most common when working with virtual machines. Managed disk is a virtual hard disk for which Azure manages all the required physical infrastructure.
Unmanaged disks (not widely used anymore)
VHDs are stored in Storage accounts as page blobs. With unmanaged disks you create and maintain this storage account manually (keep track os IOPS limits, security and RBAC access).

### Disk types (options)
After deciding your storage, you have to pick the best disk - virtual hard disks (VHDs) type for your virtual machine.

### Disk performance measures
Performance is expressed in two key measures:
* Input/output operations per second (IOPS). Measures the rate at which the disk can complete a mix of read and write operations (high performance disks have higher IOPS values)
* Throughput (data transfer). the rate at which data can be moved onto and off the disk to the host computer, measured by MBps (high performance disks have higher throughput)

**Ultra SSD**
Provides the highest disk performance available in Azure, which includes high throughput, high IOPS, and low latency.
**Premium SSD**
Provides high throughput and IOPS with low latency, and is available in all regions and can be used with VMs outside of availability zones.
**Standard SSD**
A cost-effective storage option for VMs that need consistent performance at lower speeds.
**Standard HDD**
Data is stored on conventional magnetic disk drives with moving spindles. Disks are slower and speeds are more variable than SSDs, used in development or test environments.

## Azure Storage redundancy
Azure Storage enables replication of data for increased availability and resilience.

### Data redundancy options for Azure Storage
There are several options for replication, the choice depends on the level of resilience you need.
* Locally redundant storage (LRS). copies data three times across separate racks in a datacenter
* Geo-redundant storage (GRS). data is copied three times within one region, and three times in secondary region. With GRS, the secondary region isn't available for read access until the primary region fails. If you want to read from the secondary region, even if the primary region hasn't failed, use RA-GRS for your replication type.
* Zone-redundant storage (ZRS). copies data in three storage clusters in a single region. Each cluster is in a different physical location and is considered a single availability zone
* Geo-zone-redundant storage (GZRS). combines high availability of ZRS with GRS. Data is copied across three availability zones in one region, and three times in another region.
* Read-access-geo-zone-redundant storage (RA-GZRS). same replication method as GZRS, but lets you read from the secondary region.
* Paired regions. where an Azure region is paired with another in the same geographical location.
	
https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy?WT.mc_id=thomasmaurer-blog-thmaure

## Secure your Azure Storage account
Security benefits for data in the cloud:
* protect data at rest
* protect data in transit
* support browser cross-domain access (CORS)
* control who can access data
* audit storage access

### Storage Account Keys (shared keys) (internal use)
Storage accounts can create authorized apps in AD to control access to the data in blobs and queues. For other storage models, clients can use a shared key (storage account keys). Azure creates two keys (primary and secondary for each storage account you create. The keys gives access to everything in the account.

### Shared access Signatures (SAS) (external use)
SAS is a string that contains a security token that can be attached to a uri. It deletes access to storage objects, and specifies constraints, such as permissions and the time range of access.
Types of shared access signatures:
* service-level shared access signature, allows access to specific resources in a storage account.
* account-level shared access signature, same as service-level plus additional resources and abilities
Typically use SAS for a service when a users need read and write their data to your storage account.

## Azure Storage Explorer
With Azure Storage explorer, it’s easy to read and manipulate data such as files, tables, and messages. Storage Explorer is a GUI application to simplify access to, and the management of data stored in Storage Accounts.
Azure Storage Explorer can access many different data types from services like these:
* Azure Blob Storage. Blob storage is used to store unstructured data as a binary large object (blob).
* Azure Table Storage. Table storage is used to store NoSQL, semi-structured data.
* Azure Queue Storage. Queue storage is used to store messages in a queue, which can then be accessed and processed by applications through HTTP(S) calls.
* Azure Files. Azure Files is a file-sharing service that enables access through the Server Message Block protocol, similar to traditional file servers.
* Azure Data Lake Storage. Azure Data Lake, based on Apache Hadoop, is designed for large data volumes and can store unstructured and structured data.

## Azure Files
A cloud-based file share hosted in Azure for storing and sharing files in an Azure Storage account.

### Data access method
Two built-in methods of data access support by Azure Files:
* direct access via mounted drive
* Use Windows Server (either on-premise or Azure) and install Azure File Sync to synchronize the files between local shared and Azure Files
Azure File Sync allows you to extend your on-premise file share into Azure by using the file server as a local cache for your Azure file share. A software-based agent is installed on the file server and communicates with the Storage Sync Service. 

### File redundancy option
Because Azure Files stores files in a storage account, you can choose between:
* standard performance storage accounts - uses HDD to store data, lower cost and performance
* premium performance storage accounts - uses SSD to store data, higher cost and performance

### Secure access to files stored in Azure Files
- Configure IP-based firewall rules
- Enable and use Azure AD AS authentication
- Share snapshots

## Azure Import/Export
A way for an organization to export data from Azure Storage to an on-premise location. An offline solution designed to handle more data than would be feasible to transmit over a network connection. Also used to import data to Azure Storage from an on-premises location — recommended to use Azure Data Box if your region supports it because it’s easier than Azure Import/Export.

An Azure service used to migrate large quantities of data between an on-premises location and an Azure Storage account. You send and receive actual physical disks that contain your data between you on-premise location and an Azure datacenter. These disk drives can be SATA, HDDs, or SSDs, and ideally suites where you must upload/download large amounts of data.
- import, use WAImportExport tool, which checks and prepares a journal file
- export, Microsoft copies the data from Azure Blob storage to your physical disks
You typically use this service to:
* Migrate large amounts of data from on-premises to Azure, as a one-time task
* Back up your data on-premises in Azure Storage
* Recover large amounts of data that you previously stored in Azure Storage
* Distribute data from Azure Storage to customer sites
Must have items to support the export process:
* An active Azure subscription and an Azure Storage account holding your data in Azure Blob storage
* A system running a supported version of Windows
* BitLocker enabled on the Windows system
* WAImportExport version 1 downloaded and installed from the Microsoft Download Center
* An active account with a shipping carrier like FedEX or DHL for shipping drives to an Azure datacenter
* A set of disks that you can send to an Azure datacenter on which to copy the data from Azure Storage

## Azure File Sync
Azure File Sync allows you to extend your on-premises file share.

### Terminology
There are some terms you need to understand to use Azure File Sync.
* Storage Sync Service is the high-level Azure resource for Azure File Sync. The service is a peer of the storage account, and it can also be deployed to Azure resource groups.
* A sync group outlines the replication topology for a set of files or folders. All endpoints located in the same sync group are kept in sync with each other. If you have different sets of files that must be in sync and managed with Azure File Sync, you would create two sync groups and different endpoints.
* A registered server represents the trust relationship between the on-premises server and the Storage Sync Service. You can register multiple servers to the Storage Sync Service. But a server can be registered with only one Storage Sync Service at a time.
* Azure File Sync agent is a downloadable package that enables Windows Server to be synced with an Azure file share. The agent has three components:
    * FileSyncSvc.exe. Service that monitors changes on endpoints.
    * StorageSync.sys. Azure file system filter driver.
    * PowerShell management cmdlets.
* A server endpoint represents a specific location on a registered server, like a folder on a local disk. Multiple server endpoints can exist on the same volume if their paths don't overlap.
* The cloud endpoint is the Azure file share that's part of a sync group. The whole file share syncs and can be a member of only one cloud endpoint. An Azure file share can be a member of only one sync group at a time.
* Cloud tiering is an optional feature of Azure File Sync that allows frequently accessed files to be cached locally on the server. Files are cached or tiered according to the cloud tiering policy you create.
https://docs.microsoft.com/en-us/learn/modules/extend-share-capacity-with-azure-file-sync/2-what-azure-file-sync

https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-planning

### Deployment process
The following steps describe the high-level process you use to set up Azure File Sync.
1. Evaluate your on-premises system: Run the evaluation cmdlet on your on-premises server to check whether your OS and file system are supported.
2. Create Azure resources: You need a storage account to contain a file share, a Storage Sync Service, and a sync group. Create the resources in that order.
3. Install the Azure File Sync agent: Install the agent on each file server that's taking part in replication to the Storage Sync Service.
4. Register the Windows Server computer with the Storage Sync Service: After you install the sync agent, you're prompted to register the server with the Storage Sync Service.
5. Create the server endpoint: After the server is registered, you add it as an endpoint in the sync group.

### Onboarding with Azure File Sync
The recommended steps to onboard on Azure File Sync for the first time with zero downtime while preserving full file fidelity and access control list (ACL) are as follows:
1. Deploy a Storage Sync Service.
2. Create a sync group.
3. Install Azure File Sync agent on the server with the full data set.
4. Register that server and create a server endpoint on the share.
5. Let sync do the full upload to the Azure file share (cloud endpoint).
6. After the initial upload is complete, install Azure File Sync agent on each of the remaining servers.
7. Create new file shares on each of the remaining servers.
8. Create server endpoints on new file shares with cloud tiering policy, if desired. (This step requires additional storage to be available for the initial setup.)
9. Let Azure File Sync agent do a rapid restore of the full namespace without the actual data transfer. After the full namespace sync, sync engine will fill the local disk space based on the cloud tiering policy for the server endpoint.
10. Ensure sync completes and test your topology as desired.
11. Redirect users and applications to this new share.
12. You can optionally delete any duplicate shares on the servers.
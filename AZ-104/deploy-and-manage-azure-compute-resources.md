# Deploy and manage Azure compute resources

## Azure Virtual Machines
An on-demand scalable cloud computing resource.

## Adding size disks in Virtual Machines
VMs use disks for three different purposes:
* Operating system storage - includes one disk that stores the operating system
* Temporary storage - includes a temporary VHD used for page and swap files
* Data storage - attached to a VM ti store files, databases, and other data

### Storing VHD files
In Azure, VHDs are stored in an Azure storage account as a **page blobs**. Below shows the various kinds of storage accounts and which objects can be used within each:
Storage account type	Services supported	Types of blobs supported
General-purpose standard	Azure Blob storage, Azure Files, Azure Queue storage	Block blobs, page blobs, and append blobs
General-purpose premium	Blob storage	Page blobs
Blob storage, hot and cool access tiers	Blob storage	Block blobs and append blobs

### Attach data disks to VMs
You can add data disks to a VM at any time by attaching them to the VM.
Note: The VHD can’t be delete from storage while it’s attached

### Types of disks
Azure Disks are designed for 99.999% availability.

There are 4 performance tiers for storage that you can choose from
**Ultra disks**. delivers high throughput, high IOPS, and consistent low disk storage for Azure IaaS VMs. Ultra disks include the ability to dynamically change the performance of the disks without the need to restart the VM (only for data disks).
**Premium SSD disks**. delivers reliable high-performance, and low-latency disk support for VMs running I/O - intensive workloads (limited to specific VM sizes).
**Standard SSD**. for low-end VM, but provides performance and cost perspective (only available in specific regions and only with managed disks).
**Standard HDD storage**. backed by traditional HDDs, but billed at a lower rate.

Note: you can only resize a disk to a large size. Shrinking managed disks is not supported.

### Unmanaged vs managed disks
With unmanaged disks, you are responsible for the storage accounts that are used to hold the VHDs that correspond to your VM disks. You pay the storage account rates for the amount of space you use. A single storage account has a fixed rate limit of 20,000 I/O operations/sec. This means that a single storage account is capable of supporting 40 standard VHDs at full throttle. If you need to scale out, then you need more than one storage account.

With managed disks, it’s the newer and recommended disk storage model. You specify the disk type, and the size of the disk and Azure creates and manages both the disk and the storage it uses - no need to worry about storage account rate limits.

### Data replication
The data in your Microsoft Azure storage account is automatically replicated to ensure durability and high availability. You can choose to replicate your data within the same data centre, across zonal data centers within the same region, and even across regions. There are several types of replication:
**Locally redundant storage (LRS) - Azure replicates the data within the same Azure data center. The data remains available if a node fails. However, if an entire data center fails, data may be unavailable.
**Geo-redundant storage (GRS)** - Azure replicates your data to a second region that is hundreds of miles away from the primary region. If your storage account has GRS enabled, then your data is durable even if there's a complete regional outage or a disaster in which the primary region isn't recoverable.
**Read-access geo-redundant storage (RA-GRS)** - Azure provides read-only access to the data in the secondary location, and geo-replication across two regions. If a data center fails, the data remains readable but can't be modified.
**Zone-redundant storage (ZRS)** - Azure replicates your data synchronously across three storage clusters in a single region. Each storage cluster is physically separated from the others and resides in its own availability zone (AZ). With this type of replication, you can still access and manage your data in the event that a zone becomes unavailable.
**Geo-zone-redundant storage (GZRS)** - Azure replicates your data synchronously across three availability zones in one region. Data is also replicated three times to another secondary region that's paired with it.
**Read-access geo-zone-redundant storage (RA-GZRS)** - Azure provides read-only access to the data in the secondary location. Geo-replication is across three availability zones in two region. If a data center fails, the data remains readable but can't be modified.
Note: Standard storage accounts support all replication types, but premium storage accounts only support locally redundant storage (LRS). Since VMs themselves run in a single region, this restriction isn't usually an issue for VHD storage.

## Securing your Azure virtual machine disks
Encryption is about converting meaningful information into something that appears meaningless, such as a random sequence of letters and numbers. The process of encryption uses some form of key as part of the algorithm that creates the encrypted data. A key is also needed to perform the decryption. Keys may be symmetric, where the same key is used for encryption and decryption, or asymmetric, where different keys are used. An example of the latter is the public-private key pairs used in digital certificates.

### Azure disk encryption technologies
The main encryption-based disk protection technologies for Azure VMs are:
**Storage Service Encryption (SSE)**, is performed on the physical disks in the data center. It is built into Azure and used to protect data at rest.. Azure storage platform automatically encrypts data before it’s stored to several storage services, including managed disks. (uses 256-bit AES). By default it is enabled for all new and existing storage accounts and cannot be disabled. Managed by the storage account administrator (does not affect performance)
* Azure Disk Encryption (ADE)**, encrypts the virtual machines VHDs. if VHD is protected with ADE, the disk image will only be accessible by the virtual machine that owns the disk. It controls the encryption of Windows (BitLocker) and Linux (DM-crypt) VM-controlled disks. ADE ensures that all data on VM disks are encrypted at rest in Azure, and is required for VMs backed up to the Recovery Vault. With ADE, VMs boot under customer-controlled keys and policies. ADE is integrated with Azure Key Vault for the management of these disk-encryption keys and secrets. Managed by the VM owner (does not support Basic tier VMs, and cannot use an on-premises key management service (KMS))
Note: it’s possible to use both services to protect your data.

### Encrypt existing VM disks
Before you can encrypt your VM disks, you need to:
1. Create a key vault.
2. Set the key vault access policy to support disk encryption.
    - Azure needs access to the encryption keys or secrets in your key vault to make them available to the VM for booting an decrypting volumes. There are three policies you can enable:
        1. **Disk encryption** - Required for Azure Disk encryption.
        2. **Deployment** - (Optional) Enables the Microsoft.Compute resource provider to retrieve secrets from this key vault when this key vault is referenced in resource creation, for example when creating a VM.
        3. **Template deployment** - (Optional) Enables Azure Resource Manager to get secrets from this key vault when this key vault is referenced in a template deployment.
3. Use the key vault to store the encryption keys for ADE.

### Automate secure VM deployments by adding encryption to Azure Resource Manager templates
Resource Manager templates are JSON files used to define a set of resources to deploy to Azure. For some Azure resources, including VMs you can use Azure portal to generate them, or write them from scratch.ARM templates can be saved for reusability to create identical machines later on.

## Azure virtual hard disk
A virtual hard disk is conceptually similar to a physical hard disk. You can use a VHD to host the operating system and run a virtual machine. A VHD can also hold databases and other user-defined folders, files, and data. A VHD can hold anything that you can store on a physical hard disk

### What is a virtual machine image?
A virtual machine image is a template from which you can create VHDs to run a virtual machine. The VHDs for a typical virtual machine image contain a preconfigured version of an operating system. The Azure Marketplace supplies many virtual machine images that can be used as starting points.

### What is a generalized image?
You can create your own custom virtual machine image in one of two ways:
* **Hyper-V** to create a blank virtual disk then create a virtual machine with this disk
* **Azure Marketplace** to build a virtual machine by using an existing image, which provides the OS and base functionality
After you build and customize a virtual machine, you can save the new image as a set of VHDs. The tools for preparing a virtual machine for generalization vary by operating system:
* for building Windows image - Microsoft System Preparation (Sysprep) tool
* for building Linux image - Windows Azure Linux Agent (waaent) tool
Note: using a generalized image you have to supply items such as the hostname, user account details, and other information that the generalized process removed.

### What is a specialized virtual image?
A specialized virtual image is a copy of a live virtual machine after it has reached a specific state (e.g. a specialized image might contain a copy of the configured operating system, software, user accounts, databases, connection information, and other data).

## Azure Resource Manager templates
A Resource Manager template precisely defines all the Resource Manager resources in a deployment. You can deploy a Resource Manager template into a resource group as a single operation. A Resource Manager template is a JSON file, making it a form of declarative automation, meaning that you define what resources you need but now how to create them (i.e. you define what you need and it is Resource Manager’s responsibility to ensure that resources are deployed correctly). Below are the benefits:
* Template improve consistency
* Templates help express complex deployments
* Templates reduce manual, error-prone tasks
* Templates are code
* Templates promote reuse
* Templates are linkable
Below is what is contained in a Resource Manager template:
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}

Azure Quickstart templates in Azure portal:
https://docs.microsoft.com/en-us/learn/modules/build-azure-vm-templates/3-discover-quickstart-templates?pivots=windows-cloud

### What’s Azure Resource Manager?
Azure Resource Manager is the interface for managing and organizing cloud resources (i.e. it is a way to deploy cloud resources).
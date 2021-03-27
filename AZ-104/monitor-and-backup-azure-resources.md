# Monitor and backup Azure Resources

## Azure Security Center
A service that manages the security of your infrastructure from a centralized location. Use Security Center to monitor the security of your workloads, whether they’re on-premises or on Azure.
Security Center provides tools that:
* improve your protection against security threats - monitor the health of your resources and implement recommendations
* ease the configuration of your security - it’s natively integrated with other Azure services
Security Center creates an agent on each supported virtual machine as its created. It then automatically starts collecting data from the machine.

### Criteria for assessing Azure Security Center.
* You want to identify and address risks and threats to your infrastructure.
* You don't have the traditional in-house skills and capital needed to secure a complex infrastructure.
* You want to secure an infrastructure that consists of on-premises and cloud resources.

### Protect against threats with Azure Security Center
Use access and application controls in Security Center to help protect your resources. These controls block suspicious activity (e.g. you protect your virtual machines through Just-in-time (JIT) virtual machine access, which blocks persistent access to virtual machines).

### Respond to threats with Azure Security Center
Security Center helps you respond to threats faster, and in an automated way, through playbooks. Playbooks are automated procedures that you run against alerts

## Azure Application Insights
Monitor and manage the performance of your applications. Application Insights automatically gathers information related to performance, errors, and exceptions in applications. It can also be used to diagnose what caused the problems that affect an applications.

### Criteria for assessing Application Insights
You use Application Insights if:
* You want to analyze and address issues and problems that affect your application's health.
* You want to improve your application's development lifecycle.
* You want to analyze users' activities to help understand them better.

### Integrate Application Insights with your applications
To integrate Application Insights with applications, you set up an Application Insights resource in Azure portal, then install an **instrumentation package** in your application - this package will monitor your application and send log data to the Log Analytics workspace.

### Monitor your applications continuously
Application Insights can send alerts for issues like failures or unavailability of your application. You can create **availability tests** to monitor the health of your applications continuously, also allows you to check the health of your application from different geographic locations.

### Continuously monitor your release pipelines
Application Insights works with Azure pipelines. Use them together to improve your development lifecycle. When an Azure Pipelines release pipeline receives an alert from Application Insights that something went wrong, it can stop the deployment.

## Azure Monitor
Service for collecting, combining, and analyzing data from different sources.
- All the application log data that Application Insights collects is stored in a workspace that Azure Monitor can access.
- Virtual machines and other resources security-related data that Security Center collects is also stored in a workplace that Azure Monitor can access.
You use Azure Monitor if:
* You need a single solution to help you collect, analyze, and act on log data from both cloud and on-premises.
* You're using services such as Azure Application Insights and Azure Security Center. Those services store their collected data in workspaces for Azure Monitor. You can then use Azure Monitor Log Analytics to interactively query the data.

### Benefits of Azure Monitor
Azure Monitor centralizes and combines your metrics and log data from different sources. Azure monitor requires little to no configuration to get started. Azure Monitor automatically makes log data available to you from your virtual machines. That’s because Log Analytics Agent in Azure Security Center automatically collects all the data into a workspace for Azure Monitor.

### Log queries
You use the Kusto Query Language to query log information for your services running in Azure. A Kusto query is a read-only request to process data and return results

## Alerting
Azure Monitor can use thresholds to monitor specific resources. It’s far more useful to be notified when the free disk space on a server is less than 5 percent, instead of being alerted every time a file is saved.

You use metric alerts to achieve regular threshold monitoring of Azure resources. Azure Monitor runs metric alert trigger conditions at regular intervals. When the evaluation is true, Azure Monitor sends a notification. Metric alerts can be useful if, for instance, you need to know when your server CPU utilization is reaching a critical threshold of 90 percent. You can be alerted when your database storage is getting too low, or when network latency is about to reach unacceptable levels.

Note: all alerts are governed by their rules. For metric alerts, there's an additional factor to define: the condition type. It can be static or dynamic.

### Using dynamic threshold metric alerts
Dynamic metric alerts use machine learning tools that Azure provides to automatically prove the accuracy of the thresholds defined by the initial rule. There's no hard threshold in dynamic metrics. However, you'll need to define two more parameters:
* The look-back period defines how many previous periods need to be evaluated. For example, if you set the look-back period to 3, then in the example used here, the assessed data range would be 30 minutes (three sets of 10 minutes).
* The number of violations expresses how many times the logic condition has to deviate from the expected behaviour before the alert rule fires a notification. In this example, if you set the number of violations to two, the alert would be triggered after two deviations from the calculated threshold.

## Application Insights
An Azure service that helps you to monitor the performance and behaviour of web applications. It captures two kinds of data:
* **events** which are individual data points that can represent any kind of even that occurs in an app.
* **metrics** are measurements of values, typically taken at regular intervals, that aren’t tied to a specific event.

## Azure Backup
A built-in service that provides secure backup for all Azure-managed data assets. It uses zero-infrastructure solutions to enable -self-service backups and restores, with at-scale management at a lower and predictable cost. Azure Backup offers specialized backup solutions for Azure and on-premises virtual machines. Also enabled workloads like SQL Server and SAP HANA running in Azure VMs to have enterprise-class backup and restore options.
* Need daily backups
    * Azure Virtual Machine
    * Azure File Share
    * SQL Server (runs in a VM)
    * SAP HANA (runs in a VM)
* Azure provides a backup automatically
    * Azure SQL Database

### Azure Backup vs Azure Site Recovery
Both aim to make system more resilient to faults and failures. However, the primary goal backup is the maintain copies of stateful data that allow you to go back in time (e.g. accidental data loss, data corruption, etc), **site-recovery** replicates the data in almost real time (between regions), and allows for a failover (e.g. region-wide disaster)

### Recovery Services vault
Azure Backup uses a Recovery Services vault to manage and store the backup data. A vault is a storage-management entity, which provides a simple experience to carry out and monitor backup and restore operations - the vault acts as a RBC boundary to allow secure access to the data.

## Azure Site Recovery
Azure Site Recovery replicates your virtual machine workloads between Azure regions. You can also use Site Recovery to migrate VMs from other environments, such as on-premises infrastructure, to Azure. It’s designed to replicate workloads from a primary site or region, to a secondary site. Site Recovery protects your VM instances in Azure automatically. Site Recovery mirrors the source VM configuration and creates required or associated resource groups, storage accounts, virtual networks, and availability sets to a secondary Azure region. The resources created are appended with a Site Recovery suffix.

### Replication to a secondary region
Installing the Site Recovery mobility service happens when you enable replication for an Azure VM. The installed extension registers the VM with Site Recovery. Continuous replication of the VM then begins, with any writes to the disk immediately transferred to a local storage account. Site Recovery uses this account, replicating the cache to a storage account in the destination environment.

### Disaster recovery drill
A DR drill is a way to check if you configured your solutions correctly. The drill should give you and your company, confidence that your data and services are available if a disaster happens.

## Failover and failback using Azure Recovery
Azure Site Recovery enables your organization to have flexibility - either manually failing over to a secondary Azure region, or failing back to a source virtual machine.

### What is failover
A failover occurs when a decision is made to execute a DR plan for your organization.

### What is reprotection, and why is it important
When a VM is failed over, the replication performed by Site Recovery is no longer occurring. you have to re-enable the protection to start protecting the failed-over VM.

### What is failback
Failback is the reverse of failover. It’s where a completed failover to a secondary region has been committed, and is now the production environment

More info on Failover, Reprotection, and Failback - if needed.
https://docs.microsoft.com/en-us/learn/modules/protect-infrastructure-with-site-recovery/7-failover-and-failback
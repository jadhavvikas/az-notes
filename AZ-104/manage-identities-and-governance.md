# Managing Identities and governance

## What are user accounts in Active Directory
in Azure Active Directory (AD), a user’s account access consists of
- the type of the user (administrator, members, guest)
- their role assignment
- their ownership of objects

### Access management in Azure AD
* Azure AD Roles to manage Azure AD-related resources like users, groups, billing, licensing, application registrations, etc.
* RBAC for Azure Resources to manage access to resources like VMs, SQL database, storage, etc.

There are three different ways to assign access rights:
- direct assignment
- group assignment
- rule-based assignments (dynamic)

### Azure Active Directory B2B
Azure Active Directory business to business allows external team to collaborate with your internal team. You invite guest users to the Azure AD organization, group, or application, their account will then be added to Azure AD as a guest account - either invitation by email or direct link from an application.

### Why Azure AD B2B instead of federation
B2B you don’t take responsibility of managing and authenticating the credentials and identities of partner. you don’t need an IT department or AD administrator to create and manage external user accounts. A federation you have trust established with another organization, or a collection of domains.

### Azure Subscriptions
Subscriptions in Azure are both billing entity and a security boundary. Resources such as VMs, databases, storage are associated with a single subscription. Each subscription has a single account owner responsible for any charges incurred by the resources. Each subscription is associated with a single Azure AD directory. Users, groups, and applications in that directory can manage resources in the subscription.

## Password Reset in Azure AD
Self-service password reset (SSPR) allows users to fix passwords themselves. When a user goes directly to the password reset portal it takes these steps:
1. localization, the portal checks the browser’s locale setting for appropriate language
2. verification, the user enters their username and passes a captcha
3. authentication, the user enters the required data to authenticate identity
4. password reset, if authentication passes, they enter a new password
5. notification, message sent to user to confirm reset

Methods used for SSPR (enable two or more):
- mobile app notification
- mobile app code
- email
- mobile phone
- office phone
- security questions

### License requirements
The editions of Azure AD are Free, Premium P1, Premium P2. Password reset functionality depends on your edition;
- any user signed in can change their password, all
- not signed in and forgot/expired password, Premium P1 or P2
- in hybrid environment (AD on-premise and Azure AD), Premium P1 or P2

Note: Microsoft enforces a strong default two-gate password reset policy for Azure administrator role, meaning administrators don’t have the ability to use security questions - they require two pieces of authentication data, such as email address, authenticator app, or phone number.

## Azure RBAC
Azure role-based access control (Azure RBAC) is an authorization system built on Azure Resource Manager that provides access management of resources in Azure. You can grant the exact access that users need to do their job.

RBAC uses an allow model for access. When assigned to a role, RBAC allows you to perform specific actions, such as read, write, or delete.

Resource locks ensures any resource aren’t modified or deleted, by setting them to either delete or read-only.

### How does Azure RBAC work?
You control access to resources using Azure RBAC by creating role assignments, which control how permissions are enforced. To create a role assignment, you need 3 elements:
1. Security principal (who)
    - a name for a user, group, or application that you want to grant access to
2. Role definition (what you can do)
    - a collection of permissions(roles) defined in JSON file
    - lists the permissions that can be performed (read, write, delete)
https://docs.microsoft.com/en-us/learn/modules/manage-users-and-groups-in-aad/5-manage-aad-roles
Azure includes several built-in roles:
* Owner - has full access to all resources, including the right to delegate access to others.
* Contributor - create and manage all resources, but can’t grant access to others.
* Reader - can view existing Azure resources.
* User Access Administrator - manage user access to Azure resources.
* or, create your own custom role
3. Scope (where)
    - where the access applies to (Management group > Subscription > Resource group > Resource)
        - management groups help manage subscriptions by grouping them together
        - subscriptions help organize access to resources and determines how resource is billed
        - resource groups are containers that group related resources together

### Custom Roles
Custom roles allow you to define roles that can be assigned to users, groups, and service principals at the scope of Subscription, Resource group, or Resource

## Resource Groups
A resource group is a logical container for resources.
- all resources must be in a resource group
- a resource can only be a member of a single resource group

## Tags
Tags are name/value of text data that can be applied to resources and resource groups. It allows you to associate details about your resource.

## Azure Policy
Azure Policy lets you create, assign, and manage policies. These policies apply and enforce rules that your resources need to follow, such as only allowing specific types of resources to be created, or only allowing resources in a specific region.

Implementing policy ensures that all employees with Azure access are following internal standards for creating resources.

### Assess resources that can move
1. identify the resource types of resources (in All Resources)
2. Check resource types against the “move support for resources”, which shows whether each resource type can be moved between resource groups or between subscriptions, e.g.:
Can be moved	Can’t be moved
Azure Storage accounts	Azure Active Directory domain services
Azure virtual machines	Azure backup vaults
Azure virtual networks	Azure App Service gateways
** You can move virtual machines, but you have to first make sure that you can move all of its dependent resources along with it. **

### Moving guidance for App service resources
When moving a Web App across subscriptions, the follow guidelines apply:
* The destination resource group must not have any existing App Service Resources (App Service resources include:
    * Web Apps
    * App Service plans
    * Uploaded or imported TLS/SSL certificates
    * App Service Environments

#### Additional resources
Valid resources & prepare to test your move
https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/5-validate-resources

Steps to move resources between Azure resource groups
https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/6-move-verify-resources

https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/7-exercise-move-verify-resources

## Azure AD Join
With Azure AD join, you can join devices (Windows 10 or Windows Server 2019) to your Azure Active Directory organization without needing to sync with an on-premises Active Directory instance - best suited for organizations that are principally cloud-based

### Provisioning options
when deploying to Azure AD join, you have three choices:
* self-service. requires users to manually configure the device during the Windows OOBE for new devices, or by using Windows settings for older devices (suited for individuals with strong tech BG).
* windows autopilot. allows you to preconfigure Windows devices, including automatically joining the device to your Active Directory organization, automatic MDM enrolment, and creating customer OOBE content (simplifies the management and deployment of devices across your organization).
* bulk enrolment. lets you set up a provisioning package that applies to a large number of new Windows devices at the same time
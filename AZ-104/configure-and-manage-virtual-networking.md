# Configure and manage virtual networking


## Azure virtual networking (VNet)
Enables resources, such as virtual machines, web apps, and databases to communicate with: each other, users on the internet, and on-premises client computers. When creating a VNet, you must specify a custom private IP address space using public or private address. Azure assigns resources in a virtual network a private IP address from the address space that you assign.
Azure virtual network provide key networking capabilities:
* **Isolation and segmentation**
    - Azure allows you to create multiple isolated virtual networks, by defining a private IP address space, using either public or private IP address ranges. You can then segment that IP address space into subnets, and allocate part of the defined address space to each named subnet.
* **Internet communications**
    - A VM in Azure can connect out to the internet by default. You can enable incoming connections from the internet by defining a public IP address or public load balancer
* **Communicate between Azure resources**
    - You’ll want to enable Azure resources to communicate security with each other:
        * **Virtual networks**, can connect Azure resources together, such as VMs App services, AKS, etc.
        * **Service endpoints**, connect other Azure types, such as Azure SQL databases, and storage accounts, by enabling you to link multiple Azure resources to virtual networks, thereby improving security and providing optimal routing between resources.
* **Communicate with on-premises resources**
    - Enables you to link resources together in your on-premises environment and within your Azure subscription, in effect creating a networking that spans both local and cloud environments. There are three mechanisms to achieve this:
        * **Point-to-site Virtual Private Networks**, a VPN connection that a computer outside your organization makes back into your corporate network, expect that it works in the opposite direction  (i.e. the client computer initiates an encrypted VPN connection to Azure). https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal
        * **Site-to-site (IPsec) Virtual Private Networks**, manually link your on-premises VPN device or gateway to the Azure VPN gateway in a virtual network (in Azure it appears as being on the local site).
        * **Azure ExpressRoute**, for environments where you need greater bandwidth and even higher levels of security. It provides dedicated private connectivity to Azure and does not travel over the internet.
* **Route network traffic**
    - By default Azure will route traffic between subnets on any connected virtual network, on-premises networks, and the internet. You can control or override those settings:
        * **Route tables**, allows you to define rules as to how traffic should be directed. Create custom route tables that control how packets are routed between subnets
        * **Border Gateway Protocol (BGP)**, works with Azure VPN Gateways or ExpressRoute to propagate on-premises BGP routes to Azure virtual networkshttps://www.cloudflare.com/en-ca/learning/security/glossary/what-is-bgp/
* **Filter network traffic**
    - Enables you yo filter network traffic between subnets by using using:
        * **Network security groups**, that can contain multiple inbound and outbound security rules that allow or block traffic based on factors such as source and destination IP address, port, and protocol.
        * **Network virtual appliances**, is a specialized VM that can be compared to hardened network appliance. It carries out a particular network function, such as running a firewall or performing WAN optimization.
* **Connect virtual networks**
    - You can link virtual networks together using **virtual network peering**. Peering enables resources in each virtual network to communicate with each other (can be in separate regions to create a global interconnected network through Azure).
    - Configuring a VNet-to-VNet connection is a simple way to connect VNets in different regions/subscriptions. Similarly to creating a Site-to-Site IPsec connection to an on-premises, you can connect a virtual network to another virtual network. Both use a VPN gateway to provide a secure tunnel with IPsec/IKE. When you create a **VNet-to-VNet** connection, the local network gateway address space is automatically created and populated. If you update the address space for one VNet, the other VNet automatically routes to the updated address space. It's typically faster and easier to create a VNet-to-VNet connection than a Site-to-Site connection (you need to have a special subnet (gateway subnet reserved at /27 /28 CIDR blocks)

## Azure VPN Gateway
Azure virtual network gateway provides an endpoint for incoming connections from on-premises locations to Azure over the network. VPN gateways are deployed in Azure virtual networks and enable the follow connectivity:
* Connect on-premises datacenter to Azure virtual networks through a site-to-site connection.
* Connect individual devices to Azure virtual networks through a point-to-site connection.
* Connect Azure virtual networks to other Azure virtual networks through a network-to-network connection.
Each virtual network can have only one VPN gateway. All connections to the VPN gateway share the available network bandwidth. Within each virtual network there are two or more VMs. These VMs have been deployed to a special subnet that you specify, called **gateway subnet** — contains routing tables for connections to other networks, along with specific gateway services.

A key setting is the **gateway type**, which determines the way the gateway functions. For a VPN gateway, options include:
* Network-to-network connections over IPsec/IKE VPN tunnelling, linking VPN gateways to each other.
* Cross-premises IPsec/IKE VPN tunnelling, for connecting on-premises networks to Azure through dedicated VPN devices create site-to-site connections.
* Point-to-point connections over IKEv2 or SSTP, to link client computers to resources in Azure.

### Plan a VPN gateway
Three architectures to consider:
* Point-to-site over the internet
* Site-to-site over the internet
* Site-to-site over a dedicated network, such as Azure ExpressRoute

### Planning factors
Factors that you need to cover during the planning process include:
* Throughput - Mbps or Gbps
* Backbone - Internet or private?
* Availability of a public (static) IP address
* VPN device compatibility
* Multiple client connections or a site-to-site link?
* VPN gateway type
* Azure VPN Gateway SKU

When you deploy a VPN gateway, you specify the VPN type: either **policy-based** or **route-based**, which determines how the VPN traffic is to be encrypted.
* **Policy-based VPN gateways**, specify statically the IP address of packets that should be encrypted through each tunnel. This type of device evaluates every data packet against those sets of IP addresses to choose the tunnel where that packet is going to be sent through. Key features include:
    * support IKEv1 only
    * use static routing, where combinations of address prefixes from both networks control how traffic is encrypted and decrypted through the VPN tunnel
    * must be used in specific scenarios that require them, such as compatibility with legacy on-premises VPN devices
* **Route-based VPN gateways**, IPSec tunnels are modelled as a network interface or VTI (virtual tunnel interface). IP routing decide across which one of these tunnel interfaces to send each packet. This is the preferred connection method for on-premises devices. Use this if you need any:
    * connection between virtual networks
    * point-to-point connections
    * multisite connections
    * coexistence with an Azure ExpressRoute gateway.
	Key features include:
    * supports IKEv2
    * uses any-to-any(wildcard) traffic selectors
    * can use dynamic routing protocols, where routing/forwarding tables direct traffic to traffic IPSec tunnels
Both type of VPN gateways (route-based and policy-based) in Azure use pre-shared key as the only method of authentication. Both also rely on **Internet Key Exchange (IKE)** and **Internet Protocol Security (IPSec)**. IKE is used to set up a security association (an agreement of encryption) between two endpoints. The association is then passed to the IPSec suite, which encrypts and decrypts data packets encapsulated in the VPN tunnel.

more information for VPN gateways if needed
https://docs.microsoft.com/en-us/learn/modules/connect-on-premises-network-with-vpn-gateway/2-connect-on-premises-networks-to-azure-using-site-to-site-vpn-gateways

## Azure ExpressRoute
Azure ExpressRoute enables organizations to extend their on-premises networks into the Microsoft Cloud over a private connection implemented by a connectivity provider. This arrangement means that the connectivity to the Azure datacenter doesn’t go over the internet but across a dedicated link. Connections are more reliable, latency is minimal, and throughput is great increased.

An ExpressRoute circuit is create by using the Azure Portal. After the circuit has been provisioned, the Microsoft side of the circuit is ready to accept connections. The provider must configure their side of the circuit for connecting to your network - sending the provider the value of the **Service key** field enables them to configure the connection (can take several days).

More information on ExpressRoute circuit/peering
https://docs.microsoft.com/en-us/learn/modules/connect-on-premises-network-with-expressroute/3-how-expressroute-works

### ExpressRoute connectivity models
Connections into ExpressRoute can be through the following mechanisms:
* **IPVPN network (any-to-any)**, IPVPN providers typically provide connectivity between branch offices and your corporate datacenter over managed layer 3 connections. With ExpressRoute, the Azure datacenters appear as if they were another branch office.
* **Virtual cross-connection through an ethernet exchange**, if your organization is co-located with a cloud exchange facility, you request cross-connections to the Microsoft Cloud through your provider’s Ethernet exchange. These cross connections to the Microsoft Cloud can operate at either layer 2 or layer 3 (in the OSI model)
* **Point-to-point ethernet connection**, provide layer 2 or managed layer 3 connections between your on-premises datacenters or offices to the Microsoft Cloud

### What is layer 3 connectivity?
Microsot uses industry standard dynamic routing protocol (BGP) to exchange routes between your on-premises network, your instances in Azure, and Microsoft public addresses. Microsoft establishes multiple BGP sessions with your network for different traffic profiles.

### How ExpressRoute works
It uses a combination of ExpressRoute circuits and routing domains to provide high-bandwidth connectivity to the Microsoft Cloud.
1. An **ExpressRoute circuit** is the logical connection between your on-premises infrastructure and the Microsoft Cloud
2. ExpressRoute circuits then map to **routing domains**, with each ExpressRoute circuit having multiple routing domains
3. **Azure private peering** connects to Azure compute services (VMs, and other cloud services that are deployed with a virtual network)
4. **Microsoft Peering** supports connections to cloud-based SaaS offerings, such as Microsoft 365 and Dynamics 365

Additional information
https://docs.microsoft.com/en-us/learn/modules/configure-network-for-azure-virtual-machines/6-describe-azure-expressroute

More information on features and benefits of ExpressRoutehttps://docs.microsoft.com/en-us/learn/modules/connect-on-premises-network-with-expressroute/2-expressroute-service


## Designing an IP address schema
Comparing on-premises network design with Azure network design

### On-premises IP addressing
A typical on-premises network design includes:
* routers
* firewalls
* switches
* network segmentation

### Azure IP addressing
In a typical Azure network design, it usually has:
* virtual networks
* subnets
* network security groups
* firewalls
* load balancers

Three ranges of non-routable IP addresses that are designed for internal networks that won’t be sent over internet routers:
* 10.0.0.0 to 10.255.255.255
* 172.16.0.0 to 172.31.255.255
* 192.168.0.1 to 192.168.255.255

### Basic properties of Azure virtual networks
By default, all subnets in an Azure virtual network can communicate with each other. However, you can use a network security group to deny communication between subnet (subnet - smallest /29, largest /8).

### Public and private IP addressing in Azure
Constraints and limitations for public and private IP addresses in Azure.

### IP address types:
Two types of IP addresses you can use in Azure:
* Public IP addresses
    * use for public-facing services
    * can be assigned to a VM, internet-facing load balancer, VPN gateway, or application gateway
    * two types of SKUs:
        * **Basic** - can be assigned by static or dynamic allocation methods, adjustable inbound originated flow idle timeout of 4-30mins, fixed outbound originated flow idle timeout of 4 mins, open by default (recommended to use NSG to restrict inbound or outbound traffic), doesn’t support availability zone scenario
        * **Standard** - always use static allocation method, adjustable inbound originated flow idle timeout of 4-30mins, fixed outbound originated flow idle timeout of 4 mins, secured by default, and closed inbound traffic, zone-redundant
* Private IP address
    * used for communication within a virtual network and on-premises networks
    * can be set to dynamic (DHCP lease) or static (DHCP reservation)
Both types of IP addresses can be allocated in one of two ways:
* Dynamic
* Static

In Azure virtual networks, IP addresses can be allocated to the following types of resources:
* Virtual machine network interfaces
* Load balancers
* Application gateways

## Virtual networking peering
Use virtual networking peering to directly connect Azure virtual networks. When using peering to connect virtual networks, VMs in these network communicate with each other through the use of their private IP address, as if they were in the same network.
Two types of peering connection are created in the same way:
* **Virtual network** connects virtual networks int he same Azure region
* **Global virtual network peering** connects virtual networks that are in different regions
- To connect networks using virtual network peering you have to create connections in each virtual network, called reciprocal connections (e.g. MarketVNet -> SalesVNet, SalesVNet -> MarketVnet)
- you can use virtual network peering to connect different subscriptions (Network Contributor role)
- only virtual networks can communicate with each other, virtual networks can’t communicate with the peers of their peers (e.g. A <-> B <-> C, A can’t communicate with resources in C)
- use virtual network gateways as transit points (gateway transit) to enable on-premises connectivity without deploying virtual network gateways to all your virtual networks. This method reduces cost and complexity. Using Gateway Peering, you can configure a single virtual network as a hub network.
- can also connect virtual networks together through ExpressRoute circuit.

## Network security groups and service endpoints
Network security groups (NSG) filter network traffic to and from Azure resources. NSGs contain security roles that you configure  to allow or deny inbound and outbound traffic.

### Network security group assignment
NSG are assigned to a network interface or subnet. When you assign a network security group to a subnet, the rules apply to all network interfaces in that subnet. You can restrict traffic further by associating a network security group to the network interface of a VM.

When you apply network security groups to both a subnet and a network interface, each network security group is evaluated independently. Inbound traffic is first evaluated by the network security group applied to the subnet, and then by the network security group applied to the network interface. Conversely, outbound traffic from a VM is first evaluated by the network security group applied to the network interface, and then by the network security group applied to the subnet.

Applying a network security group to a subnet instead of individual network interfaces can reduce administration and management efforts. This approach also ensures that all VMs within the specified subnet are secured with the same set of rules.

### Security rules
NSG security rules are evaluated by priority, using the 5-tuple information (source, source port, destination, destination port, and protocol) to allow or deny the traffic (e.g. a security rule to allow inbound traffic on port 3389 (RDP) to your web servers, with a priority of 200, then you create a rule to deny inbound traffic on port 3389, with a priority of 150 - the latter takes precedence, because it’s processed first (priority 150 is processed before the rule with priority 200))

### Augmented security rules
Use augmented security rules for NSGs to simplify the management of large number of rules. It helps when you need to implement more complex network sets of rules. Augmented rules let you add the following options into a single security rule:
* Multiple IP addresses
* Multiple ports
* Service tags
* App security groups

### Service tags
Use service tags to simplify NSG security even further, to allow or deny traffic to a specific Azure service.

### App security groups
App security groups let you configure network security for resources used by specific apps (e.g. you can group VMs logically, no matter what their IP address or subnet assignment). Use app security groups within a NSG to apply a security rule to a group of resources.

More information for the NSG:
https://docs.microsoft.com/en-us/learn/modules/secure-and-isolate-with-nsg-and-service-endpoints/2-network-security-groups

## Virtual network service endpoints
Use virtual network service endpoints to extend your private address space in Azure by proving direct connections to your Azure resources - service traffic remains on the Azure backbone, and doesn’t go out to the internet.

By default, Azure services are all designed for direct internet access. Creating a service endpoint can connect certain PaaS services directly to your private address space in Azure, so they act like they’re on the same virtual network — adding service endpoints doesn’t remove the public endpoint, it simply provides a redirection of traffic.

To enable a service endpoint, you must do the following two things:
1. Turn off public access to the service.
2. Add the service endpoint to a virtual network.

## Azure Bastion
Azure Bastion provides a secure remote connection from the Azure portal to Azure virtual machines over TLS. You provision Azure Bastion the same way as your VMs or to a peered virtual network. Then connect to any VM on that virtual network or a peered virtual network directly from the Azure portal.You connect to internal VMs by using the public IP of Azure Bastion, not public IP of the destination VM.

You use Azure Bastion to easily open an RDP or SSH session from the Azure portal to a VM that’s not publicly exposed - it connects to the VM over private IP.
Azure Bastion provides RDP and SSH connectivity to all VMs on same virtual network as the Azure Bastion subnet, or on a peered virtual network. You don't need to install an additional client, agent, or software to use Azure Bastion.

To connect to the VM resource by using Azure Bastion, the following roles provides the least privilege that you need:
* Reader role on the virtual machine
* Reader role on the NIC with private IP of the virtual machine
* Reader role on the Azure Bastion resource

###Deploy Azure Bastion host in Azure Portal.
Before you can deploy Azure Bastion, you need a virtual network. You can use an existing virtual network, or deploy Azure Bastion as you create a virtual network. Create a subnet in the virtual network called AzureBastionSubnet.

### Deploy Azure Bastion by using Azure PowerShell or Azure CLI
Run commands to create the following resources:
* subnet
* public IP
* Azure Bastion resource

## Azure DNS
Azure DNS is a hosting service for DNS domains that provides name resolution by using Microsoft Azure Infrastructure.

### What is DNS
Domain Name System (DNS) translates human-readable domains into known IP address (phonebook). DNS is a global directory hosted on servers around the world. Microsoft is part of that network that provides service through Azure DNS

How does DNS work
A DNS server carries out one of two primary functions:
* Maintains a local cache of recently accessed or used domain names and their IP addresses.- this cache provides a faster response to a local domain lookup request. If the DNS server can’t find the requested domain, it passes the request to another DNS server. This process is repeated until either a match is made, or the search times out.
* Maintains the key-value pair database of the IP addresses and any host or subdomain that the DNS server has authority over (often associated with mail, web, or other internet domain services)

DNS server assignment
In order for a computer, server, or other network-enabled device to access web-based resources, it must reference a DNS server.

DNS record types
The configuration information for a DNS server is stored as a file within a zone of a DNS server. Each file is called a record — record types are the most commonly created and used:
* **A** is a host record, and is the most common type of DNS record. It maps the domain or host name to the ip address.
* **CNAME** is the canonical name, or the alias for an A record. If you had different domain names that all accessed the same website, you would use CNAME.
* **MX** is the mail exchange record. It maps mail requests to your mail server, whether hosted on-premises or in the cloud.
* **TXT** is the text record. It’s used to associate text strings with a domain name. Azure and Microsoft 365 use TXT records to verify domain ownership.
Additionally, there are the following record types:
* Wildcards
* CAA (certificate authority)
* NS (name server)
* SOA (start of authority)
* SPF (sender policy framework)
* SRV (server locations)
SOA and NS records are created automatically  when you create a DNS zone using Azure DNS.

### What is Azure DNS
Azure DNS allows you to host and manage your domains by using a globally distributed name server infrastructure. It allows you to manage all of your domains by using your existing Azure credentials - Azure DNS acts as the SOA (state of authority) for the domain.

### Why using Azure DNS to host your domain
Azure DNS is built on the Azure Resource Manager service, which offers the following benefits:
* improved security
* ease of use
* private DNS domains
* alias record sets

### Security features
* RBAC
* Activity logs
* Resource locking

### Alias record sets
Alias record sets can point to an Azure resource. For example, you can set up and alias record to direct traffic to an Azure public IP address, an Azure Traffic Manager profile, or an Azure Content Delivery Network endpoint. The alias record set is support by the following DNS record types:
* A
* AAAA
* CNAME

## Routes and Network Virtual Appliance
Control traffic flow within your virtual network using custom routes.

### Azure routing
Network traffic in Azure is automatically routed across Azure subnets, virtual networks, and on-premises network - this routing is controlled by system routes, which are assigned by default to each subnet in a virtual network and/or VM that is deployed to a virtual network. You can’t create or delete system routes, you can override them by adding custom routes to control traffic flow to the next hop.
The Next hop type column shows the network path taken by traffic sent to each address prefix. The path can be one of the following hop types:
* **Virtual network**: A route is created in the address prefix. The prefix represents each address range created at the virtual-network level. If multiple address ranges are specified, multiple routes are created for each address range.
* **Internet**: The default system route 0.0.0.0/0 routes any address range to the internet, unless you override Azure's default route with a custom route.
* **None**: Any traffic routed to this hop type is dropped and doesn't get routed outside the subnet. By default, the following IPv4 private-address prefixes are created: 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16. The prefix 100.64.0.0/10 for a shared address space is also added. None of these address ranges are globally routable.

Network virtual appliance (NVA)
NVA is a virtual appliance that consists of various layers like:
* a firewall
* a WAN optimizer
* application-delivery controllers
* routers
* load balancers
* IDS/IPS
* proxies
You can deploy NVAs chosen from providers in the Azure Marketplace - such as CheckPoint, Barracuda, Sophos, etc.)

## Azure Load Balancer
Azure Load Balancer is a service you can use to distribute traffic across multiple VMs. Use Load Balancer to scale applications and create high availability for your virtual machines and services. Load balancers use a high-based distribution algorithm. By default, a five-tuple hash is used to map traffic to available servers. The hash is made from the following elements:
* **Source IP**: the IP address of the requesting client
* **Source port**: the port of the request client
* **Destination IP**: the destination IP of the request
* **Destination port**: the destination port of the request
* **Protocol type**: the specific protocol, TCP or UDP

To achieve high available with Load Balancer, you can choose to use availability sets and availability zones to ensure that virtual machines are available.
Availability set is a logical grouping that you use to isolate virtual machine resources from each other when they’re deployed, by spanning them across multiple physical servers, compute racks, storage units, and network switches

Availability zones offer groups of zone or more datacenter that have independent power, cooling, and networking - they are placed in different physical locations within the same region.
Load Balancer Product
Two products are available when you create a load balancers in Azure
* **Basic load balancers** allow:
    * port forwarding
    * automatic reconfiguration
    * health probes
    * outbound connections through source network address translation (SNAT)
    * diagnostics through Azure Log Analytics for public-facing load balancers
Note: Basic load balancers can only be used with availability sets (or vm scale set)
* **Standard load balancers** support all of basic features, plus:
    * HTTPS health probes
    * availability zones
    * diagnostics through Azure monitor, for multidimensional metrics
    * high availability (HA) ports
    * outbound rules
    * a guaranteed SLA (99.99% for two or more VMs)

### Configuring a public load balancer
A public load balancer maps the public IP address and port number of incoming traffic to the private IP address and port number of a virtual machine in the back-end pool. By applying load-balancing rules, you distribute specific types of traffic across multiple virtual machines or services.
Distribution modes:
* **Five-tuple hash**. the default distribution mode for Load balancers. The tuple is composed of source IP, source port, destination IP, destination port, protocol type.Because the source port is included in the hash and source port changes for each session, clients might be directed to a different VM for each session.
* **Source IP affinity**. also known as session affinity or IP affinity. To map traffic to the available servers, the mode uses a two-tuple hash (from the source IP address and destination IP address) or three-tuple hash (from the source IP address, destination IP address, protocol type). The hash ensures that requests from a specific client are always sent to the same virtual machine behind the load balancer.

### Internal load balancer
You can use Azure Load Balancer to distribute traffic from front-end servers evenly among back-end servers (e.g. sit between VMs and database)
You can configure an internal load balancer in almost the same way as an external load balancer, but with these differences:
* When you create the load balancer, for Type value, select Internal, this doesn’t expose the front-end IP address of the load balancer to the internet
* Assign a private address instead of a public address for the front end of the load balancer
* Place the load balancer in the protected virtual network that contains the virtual machines you want to handle the requests.
A load balancer inbound network address translation (NAT) rule to forward traffic from a specific port of the front-end IP address to a specific port of a back-end VM.

### External load balancer (public)
Operates by distributing client traffic across multiple virtual machines. An external load balancer permits traffic from the internet (e.g. between internet and VMs).

## Load balance web service traffic with Application Gateway
Application Gateway manages the requests that client applications can send to a web app. It routes traffic to a specific pool of web servers based on the URL of a request, this is known as application layer routing. The pool of web servers can be VM’s, VM scale sets, or even on-premises servers.

The way Application Gateway works is that it uses a set of rules configured for the gateway to determine where the request should go. There are two primary methods of routing traffic:
* **Path-based routing** enables you to send requests with different paths in the ULR to a different pool of back-end servers (.e.g you could direct requests with the path /video/* to a back-end pool containing servers that are optimized to handle video streaming).
* **Multiple site hosting** enables you to configure more than one web application on the same application gateway instance. In a multi-site configuration, you register DNS names (CNAMEs) for the IP address of the Application Gateway, specifying the name of each site. Application Gateway uses separate listeners to wait for requests for each site, each listener passes the requests to a different which, which can route the requests to servers in a different back-end pool (e.g. you could configure Application Gateway to direct all requests for http://contoso.com to servers in one back-end pool, and requests for http://fabrikam.com to another back-end pool) - useful for multi-tenant applications, where each tenant has its own set of VMs or other resources hosting a web application

## Monitor and troubleshoot virtual networking
Azure Network Watcher includes several tools that you can use to monitor your virtual networks and virtual machines.

### What is Network Watcher?
Network Watcher is an Azure service that combines tools in a central place to diagnose the health of Azure networks.The Network Watcher tools are divided into two categories:
* Monitoring tools
    * **Topology**, generates a graphical display of your Azure network, its resources, its interconnections, and their relationship with each other
    * **Connection monitor**, provides a way to check that connections work between Azure resources.
    * **Network performance monitor**, enables you to track and alert on latency and packet drops over time.
* Diagnostic tools
    * **IP flow verify**, tells you if packets are allows or denied for a specific virtual machine.
    * **Next hop**, you can determine how a packet gets from a VM to any destination
    * **Effective security rules**, displays all the effective NSG rules applied to a network interface
    * **Packet capture**, records all of the packets sent to and from a VM
    * **Connection troubleshoot**, check TCP connectivity between a source and destination VM. You can specify the destination VM by using an FQDN, a URI, or an IP address
    * **VPN troubleshoot**, diagnose problems with virtual network gateway connections
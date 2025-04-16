## **2. Securing communications and establishing boundary protection (~22%)**
### **2.1 Designing and configuring perimeter security. Considerations include:**
#### **2.1.1 Configuring network perimeter controls (e.g., Cloud Next Generation Firewall [Cloud NGFW] rules and policies, Identity-Aware Proxy [IAP], load balancers, and Certificate Authority Service)**
Cloud Next Generation Firewall rules and policies:
Firewall policies lets you group firewall rules so that you can update all at once. They can be hierarchical, which means that you can apply the group of rules to many VPC networks in one or more projects and you can assign it to an entire organization or folders, they can also be global network firewall policies, which means that the group of rules is applied to all regions and finally there are regional network firewall policies, for specific regions.

The default evaluation order is first evaluating VPC firewall rules and then the global and regional firewall policies.

**Identity-Aware Proxy:**

IAP provides a central authorization layer through HTTPS so you can use an application level access control instead of relying on network level firewalls. It works with signed headers (JWT Header) and users need the role IAP-secured Web App User to access the application.

IAP also works for on premises scenarios, using the IAP On-Prem Connector. When a request is sent to an on-premises app, the request is sent to the IAP, which authenticates and authorizes the user request. The IAP then routes the request to the IAP on-prem connector through a NEG to the on premises network.

IAP also provides TCP forwarding, to allow administrators to access backends. It enables administrators to access VMs through SSH and RDP.

**Load Balancers:**

Basically, a load balancer distributes the network traffic across the instances of your applications, to avoid performance issues. There are two types of load balancers: application load balancers and network load balancers.

Application Load Balancers

- External Application Load Balancer: Clients can connect from anywhere. Global (backends in multiple regions), Regional (backends in single region) or Classic (Depends on Premium Tier)

- Internal Application Load Balancer: Clients can connect if they are internal to Google Cloud. Regional or Cross-Region (multiple regions)

Proxy Network Load Balancers (Layer 4)

- External Proxy Network Load Balancer: Clients can connect from anywhere. Global (backends in multiple regions), Regional (backends in single region) or Classic (Depends on Premium Tier)

- Internal Proxy Network Load Balancer: Clients can connect if they are internal to Google Cloud. Regional or Cross-Region (multiple regions)

- External PassThrough Network Load Balancer: Balances traffic of the backends in the same region as the balancer. Clients from anywhere.

- Internal PassThrough Network Load Balancer: Balances traffic of the backends in the same region as the balancer. Clients from GCP

Application and Proxy balancers support SSL, and Proxy Load Balancers can use TCP or SSL proxies

Certificate Authority Service:

It is a service which makes it easy to configure a CA to issue digital certificates
####  **2.1.2 Setting up application layer inspection on Cloud NGFW (e.g., layer 7)**
Cloud NGFW provides the feature to analyze encrypted and unencrypted traffic for network attacks and disruption, in outbound and inbound connections, using MITM.
####  **2.1.3 Differentiating between private and public IP addressing**
Private IPs can only be used as internal IP addresses in a VPC network or in an on-prem which is connected to a VPC. Public IP addresses are routable from the Internet
#### **2.1.4 Configuring web application firewalls (e.g., Google Cloud Armor)**
Cloud Armor is a tool which protects from threats: DDoS, XSS, SQL injections, etc. It is based on security policies, which can be defined with match conditions and actions to perform on security threats (OWASP).

-   Where it works? It can be established at the Google Cloud edge to enable or deny traffic but also you can attach security policies to load balancers (all app load balancers, proxy and passthrough)
-   How it works? It works by defining a series of security policies which contain a match condition and an action to perform if the condition matches. It works also with white and blacklists.

#### **2.1.5 Deploying Secure Web Proxy**
Secure Web Proxy enables you to secure egress web traffic by configuring your clients to use the proxy as a gateway
#### **2.1.6 Configuring Cloud DNS security settings**
Domain Name System Security Extensions DNSSEC is a feature that authenticates responses to domain name lookups. It prevents attackers from poisoning the responses to DNS requests. For zones, DNSSEC manages the creation/rotation of keys and signing.
#### **2.1.7 Continually monitoring and restricting configured APls**
### **2.2 Configuring boundary segmentation. Considerations include:**
#### **2.2.1 Configuring security properties of a VPC network, VPC peering, Shared VPC, and firewall rules**
A VPC is a virtual version of a physical network which is implemented within cloud infra. VPCs are global resources while subnets are regional resources. The traffic is controlled through firewall rules which are implemented on the VMs themselves. Network administration is secured using IAM roles.

A default VPC network is created always unless you disable it, with predefined firewall rules and subnets created in all regions. You should be sure if a default VPC is suitable for your production environment or not.

Firewall rules important concepts:

-   Always least privilege principle. Block all traffic and only allow specific traffic.
-   Hierarchical firewall policy rules
-   For allow rules, restrict them to specific VMs using SAs
-   Every network with 2 implied rules: one blocking incoming connections and one allowing outgoing connections.
-   Rules are stateful: if incoming connection is allowed, response is also allowed

Types of networks:

-   Shared VPC: connect resources from different projects to the same VPC in the common host project.
-   VPC Peering: connect multiple VPCs from different projects or organizations between them.
-   Cloud VPN and Cloud Interconnect: secure connect VPCs in hybrid environments

-   Cloud VPN: HA or Classic. Used to connect on-prem to cloud using IPsec VPN
-   Cloud Interconnect: HA connections between VPCs

-   Dedicated Interconnect: direct physical connection between on-premises and GCP
-   Partner Interconnect: connection between on-premises and GCP through a service provider
-   Cross-Cloud Interconnect: direct physical connection between another cloud and GCP
#### **2.2.2 Configuring network isolation and data encapsulation for N-tier applications**
You should always isolate sensitive data in its own VPC network to meet compliance initiatives. It is easier to ensure that the VPC meet the compliance standards HIPAA or PCI-DSS.

#### **2.2.3 Identifying use cases and configuring VPC Service Controls**
VPC Service Controls are suitable for organizations that manage sensitive data and are subject to data protection regulations. This feature provides secure data exchange across organization boundaries with fine-grained control. It enables to create perimeters to protect the resources and data inside the perimeters.

When using this feature, data inside the perimeter cannot be unauthorized accessed, cannot be copied to resources outside the perimeters, and the exchange of the data is secured by ingress/ergress rules. VPC Service Controls can be configured in dry run mode to monitor requests but not taking action to them.
### **2.3 Establishing private connectivity. Considerations include:**
#### **2.3.1 Designing and configuring private connectivity between VPC networks and Google Cloud projects (Shared VPC, VPC peering, and Private Google Access for on-premises hosts)**
-   Shared VPC: You can connect an on-prem network to a shared VPC through Cloud VPN
-   VPC Peering: You can peer on-premises networks to VPC networks through Cloud VPN or Cloud Interconnect using dynamic routing (using Cloud Router)
-   Private Google Access: enables on-premises hosts to connect to Google APIs through Cloud VPN or Cloud Interconnect. You have to configure DNS, firewall rules and routes in the on-prem and VPC networks. In the on-prem network, the traffic goes through the Internet over a VPN tunnel and when it arrives the VPC network, it is sent through a route which indicates the hops to reach the Google API requested.
#### **2.3.2 Designing and configuring private connectivity and encryption between data centers and VPC network (e.g., HA VPN, Cloud Interconnect)**
-   VPN: For private connectivity, use Cloud VPN with Private Google Access. The traffic is encrypted by default.
-   Cloud Interconnect: For private connectivity, use Cloud Interconnect with Private Google Access. To encrypt traffic over Cloud Interconnect the best way is to use it with Cloud VPN

#### **2.3.3 Establishing private connectivity between VPC and Google APls (Private Google Access, Private Google Access for on-premises hosts, restricted Google access, Private Service Connect)**
-   Private Google Access: Enables API access to most Google APIs and services regardless of whether they are supported by VPC Service Controls. Choose when you don't use VPC Service Controls or you do use VPC Service Controls, but you also need to access Google APIs and services that are not supported by VPC Service Controls
-   Private Google Access for on-premises hosts: It is the same as Private Google Access but using a VPN tunnel or VLAN attachment for Cloud Interconnect.
-   Restricted Google Access: Choose only when you need access to Google APIs and services that are supported by VPC Service Controls
-   Private Service Connect: This feature enables users to use their internal IP address to access services without leaving VPC network. This provides access to Google APIs and published services
#### **2.3.4 Using Cloud NAT to enable outbound traffic**
Cloud NAT enables network address translation for outbound traffic to the internet. This enables VMs to connect to the Internet without having an external IP.

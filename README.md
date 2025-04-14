# **Exam summary**
## **1. Configuring access (~25%)**
### **1.1 Managing Cloud Identity. Considerations include:**
#### **Configuring Google Cloud Directory Sync and implement single sign-on (SSO) with a third-party identity provider.**

**Google Cloud Directory Sync**

The Google Cloud Directory Sync (GCDS) tool simplifies provisioning and deprovisioning user accounts by synchronizing Google Workspace accounts with existing Microsoft Active Directory or LDAP servers.

**Synchronization Process**

-   Export Data: Data is exported as a list from your LDAP server or Active Directory.
-   Generate Google List: GCDS connects to your Google domain and generates a list of existing Google users, groups, and shared contacts.
-   Compare and Update: GCDS compares the exported list with the Google users list and updates your Google domain to match the directory data.
-   Report: A report is emailed to specified addresses upon completion of synchronization.

**Key points**
-   **One-Way Sync**: GCDS performs one-way synchronization, ensuring the directory server data remains unchanged.
-   **Local Operation**: GCDS runs within your server environment, requiring no external access to your directory server.
-   **Auto-Provisioning/Deprovisioning**: Automatically removes user accounts from cloud apps when they are removed from the directory, reducing manual effort and security risks.

**Best practices**: for user accounts, do suspend not delete so data is not lost, and for admin accounts, do not suspend nor delete them. Use exclusion rules to prevent syncing google accounts from Google that do not exist in LDAP.

**Google Authentication: SSO**

Google Cloud supports two types of authentication: Google Authentication and Single Sign-On (SSO) authentication. These mechanisms are mutually exclusive, except for super admin accounts.

**Google Authentication**:

-   Primary method for signing in to Google Cloud.
-   Stores passwords within Google’s infrastructure.
-   Allows specification of password length and strength.

**Single Sign-On (SSO)**:

-   Supports SAML 2.0 and OpenID-compliant systems.
-   Google acts as the service provider, and your SSO system is the identity provider.
-   Users authenticate through an external identity provider, not entering passwords for Google services.
-   Advantages include better user experience, maintaining the IdP as the system of record, and no need to synchronize passwords to Cloud Identity or Google Workspace.

**SSO Configuration**:

-   In SSO with SAML setup, we need to provide a sign-in, sign-out, change password URLs and a certificate with the public key of the IdP
-   In SSO with OIDC setup, we need to configure the prerequisites of Microsoft Entra ID
#### **Managing a super administrator account**
The super admin account is used to configure the organization resource. It is recommended to keep it isolated from the Organization Administrators, which are not the same. This account has powerful permissions so it should not be used in the daily operations. To secure this account, it is recommended to: enforce 2FA, use a security key or physical authentication. The recommended practice for this type of accounts is to create only two admin accounts and store their credentials in secure locations and not use them unless a break-glass scenario occurs.
#### **Automating the user lifecycle management process**
To use this feature, the app must be configured to use SAML SSO. You also have to have Cloud Identity Premium to configure apps with this feature. If at any time you downgrade, the apps you have configured still function, but you cannot add any other app.
#### **Administering user accounts and groups programmatically**
For this we can use the directory API
#### **Configuring Workforce Identity Federation**
This lets you use an external Identity Provider IdP to authenticate and authorize. You don’t need to synchronize the user identities from the IdP to the Google Cloud identities as it would be with GCloud Directory Sync. We can use this feature with any IdP supporting OpenId Connect OIDC or SAML2.0

For example, the most common scenario is when an Active Directory is already set with all the user accounts. The most important concepts here are:

-   Active Directory serves as the Identity Provider, which means that is in charge of authenticating and authorizing users accessing Google Cloud.
-   When **provisioning** users, they are periodically synchronized (usually with GCDS) to Cloud Identity. It works one way, this means that changes in AD are replicated in GC but not the other way around

You can create workforce identity pools which are set of IdP authenticated identities so that you can group identities by departments, for example, and grant IAM permissions to the pools directly.
### **1.2 Managing service accounts. Considerations include:**
#### **Securing and protecting service accounts (including default service accounts)**
Service accounts use RSA key pairs instead of passwords. The best way to mitigate any threat is to avoid using user managed service account keys and use additional methods to authenticate service accounts.

1.  Avoid User-Managed Keys: Whenever possible, use more secure alternatives to user-managed service account keys to reduce security risks.
2.  Credential Leakage: Protect against credential leakage by ensuring service account keys are not stored in insecure locations.
3.  Privilege Escalation: Prevent unauthorized access and privilege escalation by securing service account keys properly.
4.  Information Disclosure: Avoid disclosing confidential metadata through service account keys.
5.  Non-Repudiation: Ensure that actions performed using service account keys are traceable to prevent bad actors from concealing their identity.
6.  Malicious Credential Configurations: Be cautious of malicious configurations that could bypass security defenses.
#### **Identifying scenarios requiring service accounts**
Service accounts are useful for providing non-human identities to workloads, ensuring secure and consistent access to resources.
#### **Creating, disabling, and authorizing service accounts**
This can be done through the IAM API, GCloud console or the gcloud cli tool.
#### **Securing, auditing, and mitigating the usage of service account keys**
Use whenever possible 
#### **Managing and creating short-lived credentials**
These are short lived credentials used to impersonate a service account. These can be ID tokens to provide identity, or access tokens, to provide the permissions of the service account. These tokens often come without the refresh token, so when the token is expired, the whole process has to be repeated. The tokens can be OIDC ID tokens, self-signed JWT and self-signed blobs
#### **Configuring Workload Identity Federation**
Traditionally, applications running outside Google Cloud can use [service account keys](https://cloud.google.com/iam/docs/service-account-creds#key-types) to access Google Cloud resources. However, service account keys are powerful credentials, and can present a security risk if they are not managed correctly. Workload Identity Federation eliminates the maintenance and security burden associated with service account keys.

With Workload Identity Federation, you can use Identity and Access Management (IAM) to grant to external identities [IAM roles](https://cloud.google.com/iam/docs/overview#roles), direct access on Google Cloud resources. You can also grant access through service account impersonation.

The general steps are: create the workload identity pool, then set the provider for the pool (AWS, etc.), then grant the access to the workload and then configure the workload in the provider side.
####  **Managing service account impersonation**
This is when an authenticated principal, like a user or another service account, authenticates as a service account to gain its permissions. It basically consists of the authenticated principal getting a token of the service account. They are used to temporary grant elevated permissions, test permissions, locally develop apps only working with service accounts and authenticating external apps. It is more secure than using directly the service account key. To manage it, create short-lived credentials.
### **1.3 Managing authentication. Considerations include:**
#### **Creating a password and session management policy for user accounts**
Enforce the use of strong passwords, prevent from using old passwords and explain the importance of strong passwords. Regarding to sessions, enforce frequent reauthentication for privileged users and longer sessions for certain apps
#### **Setting up Security Assertion Markup Language (SAML) and Auth**
#### **Configuring and enforcing 2-step verification**
Set up verification with the password and the phone or a security key
### **1.4 Managing and implementing authorization controls. Considerations include:**
#### **Managing privileged roles and separation of duties with Identity and Access Management (IAM) roles and permissions**
Assign different IAM roles to accounts in separate projects to enforce the principle of least privilege. This ensures that users and service accounts have only the permissions necessary for their tasks. It provides enhanced security, compliance ensuring and management improvements.
#### **Managing IAM and access control list (ACL) permissions**
In Cloud Storage, both methods are used to grant users permissions. The recommended is using IAM but both act in parallel. To only use IAM, you have to set uniform bucket-level access to disable ACLs for all cloud storage buckets. ACLs are usually useful when you want to customize access to individual objects within a bucket
#### **Granting permissions to different types of identities using IAM conditions and IAM deny policies**
We can use IAM conditions to grant access to the principals if only the conditions are met, for example, granting temporal access to fix a problem in the production environment. You can also use IAM conditions in deny policies. For example, you grant an access role to a principal but you set an IAM condition in a deny policy if the resource the principal is accessing is tagged as a prod resource.

Deny policies are used to prevent certain principals from using certain permissions, regardless of the role they have been granted.
#### **Defining access control at the organization, folder, project, and resource level using the principle of least privilege**
Basically know that the permissions are inherited from the parent resource.
#### **Configuring Access Context Manager**
It enables to grant access to principals based on the context of the request, for example, the device type, user identity or even IP address. These are called access levels.
#### **Applying Policy Intelligence**
It helps organizations to control resources and manage access. It helps with:

-   Analyze access: Policy Analyzer helps finding out which principals have access to which resources
-   Troubleshooters to help with access issues
-   Activity Analyzer: when a service account or key was used to call a Google API
-   Service Account Insight: know what SAs have not been used in the past 90 days
-   Role Recommendations: offers recommendations for least privilege responsibilities.
-   Policy Simulator: to prevent any misconfiguration and test policy changes.
#### **Managing permissions through groups**
If there are several principals that need the same set of permissions, it would then be a good a idea to create a group, then to add the principals to the group and finally assign the permissions directly to the group.
#### **Identifying use cases and configuring Privileged Access Manager (PAM)**
It is used to control temporary elevated access to selected principals and view audit logs to determine who got access to what and when. Use cases:

-   Grant emergency access: perform critical tasks without having to wait for approval
-   Control access to critical resources
-   Help secure service accounts: instead of granting roles to SAs whenever they need to perform some special task, allow them to self-elevate when needed
-   Manage access for contractors and externals

### **1.5 Defining the resource hierarchy. Considerations include:**
#### **Managing folders and projects at scale**
#### **Managing pre-built or custom organization policies for the organization, folders, and projects**
Gives centralized and programmatic control over the organization’s cloud resources. While IAM focuses on who can do some tasks, Org Policies focus on what to set the restrictions to, for example, limiting the usage of IAM service accounts. Policies can be set to dry-run mode not to deny requests but to log them to later review. They are used to prevent high-risk configurations.

#### **Using the resource hierarchy for access control and permissions inheritance**
You can set an IAM policy at the [organization level](https://cloud.google.com/resource-manager/docs/access-control-org), the [folder level](https://cloud.google.com/resource-manager/docs/access-control-folders), the [project level](https://cloud.google.com/resource-manager/docs/access-control-proj), or (in some cases) the resource level. Resources inherit the policies of the parent resource. If you set a policy at the organization level, it is inherited by all its child folder and project resources, and if you set a policy at the project level, it is inherited by all its child resources.

## **2. Securing communications and establishing boundary protection (~22%)**
### **2.1 Designing and configuring perimeter security. Considerations include:**
#### **Configuring network perimeter controls (e.g., Cloud Next Generation Firewall [Cloud NGFW] rules and policies, Identity-Aware Proxy [IAP], load balancers, and Certificate Authority Service)**
-   Cloud Next Generation Firewall rules and policies:
Firewall policies lets you group firewall rules so that you can update all at once. They can be hierarchical, which means that you can apply the group of rules to many VPC networks in one or more projects and you can assign it to an entire organization or folders, they can also be global network firewall policies, which means that the group of rules is applied to all regions and finally there are regional network firewall policies, for specific regions.

The default evaluation order is first evaluating VPC firewall rules and then the global and regional firewall policies.

-   Identity-Aware Proxy:

IAP provides a central authorization layer through HTTPS so you can use an application level access control instead of relying on network level firewalls. It works with signed headers (JWT Header) and users need the role IAP-secured Web App User to access the application.

IAP also works for on premises scenarios, using the IAP On-Prem Connector. When a request is sent to an on-premises app, the request is sent to the IAP, which authenticates and authorizes the user request. The IAP then routes the request to the IAP on-prem connector through a NEG to the on premises network.

IAP also provides TCP forwarding, to allow administrators to access backends. It enables administrators to access VMs through SSH and RDP.

-   Load Balancers:

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
####  **Setting up application layer inspection on Cloud NGFW (e.g., layer 7)**
Cloud NGFW provides the feature to analyze encrypted and unencrypted traffic for network attacks and disruption, in outbound and inbound connections, using MITM.
####  **Differentiating between private and public IP addressing**
Private IPs can only be used as internal IP addresses in a VPC network or in an on-prem which is connected to a VPC. Public IP addresses are routable from the Internet
#### **Configuring web application firewalls (e.g., Google Cloud Armor)**
Cloud Armor is a tool which protects from threats: DDoS, XSS, SQL injections, etc. It is based on security policies, which can be defined with match conditions and actions to perform on security threats (OWASP).

-   Where it works? It can be established at the Google Cloud edge to enable or deny traffic but also you can attach security policies to load balancers (all app load balancers, proxy and passthrough)
-   How it works? It works by defining a series of security policies which contain a match condition and an action to perform if the condition matches. It works also with white and blacklists.

#### **Deploying Secure Web Proxy**
Secure Web Proxy enables you to secure egress web traffic by configuring your clients to use the proxy as a gateway
#### **Configuring Cloud DNS security settings**
Domain Name System Security Extensions DNSSEC is a feature that authenticates responses to domain name lookups. It prevents attackers from poisoning the responses to DNS requests. For zones, DNSSEC manages the creation/rotation of keys and signing.
#### **Continually monitoring and restricting configured APls**
### **2.2 Configuring boundary segmentation. Considerations include:**
#### **Configuring security properties of a VPC network, VPC peering, Shared VPC, and firewall rules**
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
#### **Configuring network isolation and data encapsulation for N-tier applications**
You should always isolate sensitive data in its own VPC network to meet compliance initiatives. It is easier to ensure that the VPC meet the compliance standards HIPAA or PCI-DSS.

#### **Identifying use cases and configuring VPC Service Controls**
VPC Service Controls are suitable for organizations that manage sensitive data and are subject to data protection regulations. This feature provides secure data exchange across organization boundaries with fine-grained control. It enables to create perimeters to protect the resources and data inside the perimeters.

When using this feature, data inside the perimeter cannot be unauthorized accessed, cannot be copied to resources outside the perimeters, and the exchange of the data is secured by ingress/ergress rules. VPC Service Controls can be configured in dry run mode to monitor requests but not taking action to them.
### **2.3 Establishing private connectivity. Considerations include:**
#### **Designing and configuring private connectivity between VPC networks and Google Cloud projects (Shared VPC, VPC peering, and Private Google Access for on-premises hosts)**
-   Shared VPC: You can connect an on-prem network to a shared VPC through Cloud VPN
-   VPC Peering: You can peer on-premises networks to VPC networks through Cloud VPN or Cloud Interconnect using dynamic routing (using Cloud Router)
-   Private Google Access: enables on-premises hosts to connect to Google APIs through Cloud VPN or Cloud Interconnect. You have to configure DNS, firewall rules and routes in the on-prem and VPC networks. In the on-prem network, the traffic goes through the Internet over a VPN tunnel and when it arrives the VPC network, it is sent through a route which indicates the hops to reach the Google API requested.
#### **Designing and configuring private connectivity and encryption between data centers and VPC network (e.g., HA VPN, Cloud Interconnect)**
-   VPN: For private connectivity, use Cloud VPN with Private Google Access. The traffic is encrypted by default.
-   Cloud Interconnect: For private connectivity, use Cloud Interconnect with Private Google Access. To encrypt traffic over Cloud Interconnect the best way is to use it with Cloud VPN

#### **Establishing private connectivity between VPC and Google APls (Private Google Access, Private Google Access for on-premises hosts, restricted Google access, Private Service Connect)**
-   Private Google Access: Enables API access to most Google APIs and services regardless of whether they are supported by VPC Service Controls. Choose when you don't use VPC Service Controls or you do use VPC Service Controls, but you also need to access Google APIs and services that are not supported by VPC Service Controls
-   Private Google Access for on-premises hosts: It is the same as Private Google Access but using a VPN tunnel or VLAN attachment for Cloud Interconnect.
-   Restricted Google Access: Choose only when you need access to Google APIs and services that are supported by VPC Service Controls
-   Private Service Connect: This feature enables users to use their internal IP address to access services without leaving VPC network. This provides access to Google APIs and published services
#### **Using Cloud NAT to enable outbound traffic**
Cloud NAT enables network address translation for outbound traffic to the internet. This enables VMs to connect to the Internet without having an external IP.
## **3. Ensuring data protection (~23%)**
### **3.1 Protecting sensitive data and preventing data loss. Considerations include:**
#### **Configuring Sensitive Data Protection (SP) (e.g., discovering and redacting personally identifiable information (PII), configuring pseudonymization and format preserving encryption)**
Sensitive Data Protection helps to discover, classify and de-identify sensitive data:

-   Discovery: The discovery service creates profiles for the data in your organization
-   Inspection: The inspection service makes a deep scan of individual resources to find instances of sensitive data by specifying an infoType. The service makes a report of every instance of data that matches the infoType (credit cards, etc.).
-   Data de-identification: allows to obfuscate sensitive data. There are several methods:

-   Redaction: deletes parts or all the detected sensitive data
-   Replacement: replaces with specified data
-   Masking: replaces a number of characters with a specified character, for example “*”
-   Crypto-based tokenization (pseudonymization): encrypts the data. It includes methods for re-identifying (content.reidentify) encrypted sensitive data. Format Preserving Encryption generates an encrypted value using the same alphabet and length of the input value to not disrupt validations.

-   Deterministic encryption AES
-   Format Preserving Encryption:
-   Cryptographic Hashing HMAC-SHA256

-   Bucketing: generalizes a sensitive value replacing it with a range of values, for example, ages, temperatures, etc.
-   Date shifting: shifts sensitive date values by a random amount of time
-   Time extraction: extracts o preserves portions of date/time values

  

#### **Restricting access to Google Cloud data services (e.g., BigQuery, Cloud Storage, and Cloud SQL datastores)**

As storage services may include data which need to be secured and audited, it is important to set a governance on the data. The priorities are:

Access control:

-   Use IAM roles to control who is accessing storage resources like BQ
-   Set column-level or row-level access control to restrict access in a table to protect sensitive data from unauthorized access
-   Set VPC service controls to set a perimeter around resources containing sensitive data
-   Review and audit audit logs

Data stewardship:

-   Data masking to obscure sensitive data in a table
-   Encryption at rest and in transit
-   Use metadata to tag resources for managing purposes

Data quality:

-   Data lineage to track the flow of the data over time
-   Data profile scans to analyze characteristics of the data
-   Data quality scans to perform data checks and validations over rules

#### **Securing secrets with Secret Manager**
Secret and credential management service which lets you to store and manage sensitive data like API keys, usernames, passwords, etc. This is called a secret, but the actual value of the secret is stored in the secret version. With secret manager you can:
-   Manage rollback, recovery and auditing secret versions
-   Encrypt the secret at rest and in transit
-   Manage access to secrets with IAM roles and conditions
-   Auto rotation of secrets
-   Enforce secret residency

#### **Protecting and managing compute instance metadata**
VM metadata is stored in a metadata server to which the VM has access without authorization. It is useful for startup/shutdown scripts, maintenance, etc. Can be secured using authentication tokens.
### **3.2 Managing encryption at rest, in transit, and in use. Considerations include:**
#### **Identifying use cases for Google default encryption, customer-managed encryption keys (CMEK), and Cloud External Key Manager (EKM)**
Cloud KMS (Key Management Service) provides key management services in Google Cloud for key encryption at rest. Every data in Google Cloud is encrypted at rest, but with KMS it is encrypted at rest and you can manage how encryption keys are managed. Types of keys with their uses cases:
-   Default encryption: every data encrypted using AES-256. Google generates and manages encryption keys to which customers do not have access to.
-   Cloud KMS with sw protected keys: when you want to manage how encryption is performed (symmetric or asymmetric)
-   Cloud KMS with hw protected keys (Cloud HSM): you need keys to be stored in hardware security models of Google using FIPS 140-2 Lvl. 3
-   Cloud KMS with key import: satisfy BYOK that are generated by yourself
-   Cloud KMs with external key manager (Cloud EKM): when you need to create and control keys that are stored in a key manager which is external to Google

Normally Google manages the keys, but CMEK lets you generate, use, rotate and destroy encryption keys, which provides greater control. There is another feature called CSEK (Customer Supplied Encryption Key) which lets you supply your own encryption keys used by Google to encrypt/decrypt the data in BQ or CS. This is risky as you have to fully control all the key lifecycle. If you lose the key, you won’t be able to decrypt the encrypted data.

Envelope encryption: the CMEK is a KEK used to encrypt DEKs
#### **Determining when to use software and hardware keys**
Software keys are used generally to encrypt data in most scenarios such as applications, API keys, and general data protection. When you need to use a robust encryption with high security and you also need to meet compliance requirements. Hardware keys also offer a better performance and provides isolation, as there is physical separation. They are commonly used to encrypt sensitive data like financial data, healthcare records, etc.
#### **Creating and managing encryption keys for CMEK and EKM (e.g., key rotation and revocation, key import)**
Key Rotation:
-   Why key rotation?: to comply with security standards, avoid cryptanalysis
-   How often?: regularly. The recommended practice is to set automatic and periodic rotation at a defined period, for example, 90 days. In case you suspect a key has been compromised, rotate it manually, disable the previous one and revoke access to it ASAP.
-   How does it work?: when you rotate a key, a new version of the key is created. However, previous key version remain active and the data that was encrypted with previous versions is not re-encrypted.

Key rotation process:
1.  Create the new key for the service account
2.  Replace the old key for the new one across all apps
3.  Delete the old key

Key Revocation: when you suspect a key has been compromised, you must rotate the key to create a new version of the key, disable the key version compromised and revoke access to it, so no-one can access it and use it.
#### **Applying encryption methods to various use cases**
**CMEK:** use when you need to own your keys and control key lifecycle. It is compatible with most Google Cloud services
**CSEK:** use when you need to use your own keys generated and managed outside Google. Only compatible with Cloud Storage and Compute Engine
#### **Configuring object lifecycle policies for Cloud Storage**
This feature allows to set a TTL for objects, retaining non current versions of objects and downgrading storage classes to decrease costs. You set this at bucket level and it is made based on a series of rules to apply to actual and future objects in the bucket. For example: downgrading the storage class to objects older than a year, delete objects created before a time, or keeping just the 3 most recent versions of each object. Each rule contains an action and one or more conditions, and to apply the rule, the object must match all conditions.

You should test your rules in DEV but you can also do it in PROD by using matchesPrefix or matchesSuffix conditions in rules. The lifecycle actions are:

-   Delete
-   SetStorageClass: to downgrade storage class
-   AbortIncompleteMultipartUpload: abort when incomplete multipart upload

Bucket Lock
This feature allows to configure bucket retention policy, which means how long objects must be retained. If lock in the policy is enabled, it permanently prevents from altering the policy to be reduced or removed.
#### **Enabling Confidential Computing**
Confidential VMs are a type of Compute Engine VMs using hardware based memory encryption so data and applications cannot be read or modified while being used. They provide isolation as encrypted keys reside in dedicated hardware and attestation, as you can verify the identity and state of the VM, known as Trusted Execution Environment. Once a VM is created, it cannot be migrated to confidential, so you must set it when creating the instance.

Shielded VM: This feature enables to have Virtual Machines whose integrity has been validated in the boot or kernel level. It can be achieved through these features:

-   Secure Boot: it ensures that the system is only running authentic software by verifying the digital signature of all the components
-   Virtual Trusted Platform Module (vTPM): using BoringSSL, it enables through Measured Boot to verify the integrity of objects like keys, certificates, etc.
-   Integrity Monitoring: this relies on the measures created by Measured Boot to check and monitor the integrity of components.
### **3.3 Securing Al workloads. Considerations include:**
#### **Implementing security and privacy controls for Al/ML systems to protect against unintentional exploitation of data or models**

-   Data security:

-   Do not keep data that is not necessary for business. If possible, anonymize data.
-   Monitor all stages of data: collection, storage and transformation.
-   Implement role-based access control to data
-   Implement data encryption, secure perimeters

-   Pipelines security:

-   Use secure coding practices like dependency management, validation, sanitization during model development to avoid vulnerabilities.
-   Protect pipelines and code artifacts from unauthorized access using access-level based on roles
-   Enforce lineage and tracking assets

-   Secure Deployment: ensure the code and models run in secure environments with robust access control. Use prebuilt AI container images
-   Protect and Monitor inputs: monitor the outputs, evaluate performance and implement alerting

#### **Determining security requirements for laaS-hosted and PaaS-hosted training models**
Same as all shared responsibility model services
#### **Implementing security controls for Vertex Al**
VertexAI is a fully managed platform that helps building, deploying and scaling ML and AI applications. So that, it is very important to implement robust security controls. The most important ones are:

-   Data residency at rest
-   Uses CMEK to encrypt data
-   Use VPC Service Controls to set a secure perimeter
-   Access Transparency: logging when Google personnel access your data

These are only for VertexAI Platform, VertexAI RAG Engine only support VPC Service Controls.

## **4. Managing operations (~19%)**
### **4.1 Automating infrastructure and application security. Considerations include:**
#### **Automating security scanning for Common Vulnerabilities and Exposures (CVEs) through a continuous integration and delivery (CI/CD) pipeline**
Through Container Registry vulnerability scanning service, we can integrate security within the CI/CD pipeline lifecycle. This service is continuously scanning vulnerability databases to be up-to-date. The features of this service are:

-   Deep security scans in CI/CD pipeline: it can be integrated with Cloud Build so as soon as an image is created an stored in the Container Registry, a scan is performed
-   Binary Authorization: during deployment, you can ensure that only trusted container images are deployed in GKE or Cloud Run.

#### **Configuring Binary Authorization to secure GKE clusters or Cloud Run**
As we said, binary authorization is a product that enables to implement security measures in the supply chain of the software, when you develop and deploy container-based applications. Binary authorization can:
-   Continuously validation: periodically monitors container images
-   Enforcement: create policies to determine if an image can be deployed or not

It can be used with VPC Service Control to control copying or transferring data (policies, attestors and attestations)

#### **Automating virtual machine and container image creation (e.g., hardening, maintenance, VM patch management)**
Best practices:

1.  Package a single application per container
2.  Properly handle PID 1 and zombie processes: correctly handle signals to stop processes
3.  Optimize for the Docker build cache
4.  Remove unnecessary tools: even using distroless with just the application
5.  Build the smallest image possible
6.  Properly tag images: e.g. versioning
7.  Carefully consider whether to use a public image: maybe they are not good in production, they may be a good point to start from.

Patch management: allows to view path status of the fleet of VMs and to automate patching
#### **Managing policy and drift detection at scale (e.g., cloud security posture management, custom organization policies and custom modules for Security Health Analytics)**
-   Cloud Security Posture:
-   Custom organization policies:
-   Security Health Analytics custom modules:
### **4.2 Configuring logging, monitoring, and detection. Considerations include:**
#### **Configuring and analyzing network logs (Cloud Next Generation Firewall [Cloud  NGFW], VPC flow logs, Packet Mirroring, Cloud Intrusion Detection System [Cloud IDS],  Log Analytics)**
-   Firewall Rules logging: enabled per rule, can generate a lot of data, disabled by default and use Log Explorer
-   VPC Flow Logs: record samples of logs for network monitoring
-   Packet mirroring: clones the traffic of instances to another instance for examination. Useful to analyze security status. For example, a security tool could inspect all the mirrored traffic in search for anomalies.
-   Cloud IDS logs: to view audit logs
-   Log Analytics: uses BigQuery analytical power to analyze logs

The workflow works the following:

-   Enable logs: enable the logs that are most relevant for the organization
-   Route logs: route and aggregate logs to the desired destination (BigQuery)
-   Analyze logs: through an analytics tool, analyze the logs
#### **Designing an effective logging strategy**
For production environments logging sampling rate should desirably be 1.0 (100%)
#### **Logging, monitoring, responding to, and remediating security incidents**
Incident Response: it is the process comprehending the actions, escalations, mitigations, resolutions, and notification of any incident that impacts the confidentiality, integrity or availability of data. The process consists of:

-   Identification
-   Coordination
-   Resolution
-   Closure
-   Continuous improvement

Security Command Center:
-   Security Health Analytics: detect common vulnerabilities and misconfigurations
-   Attack Path Exposure: shows the path an attacker can follow to exploit a vulnerability
-   Event Threat Detection: produces security findings by analyzing events in Cloud Logging and matching to Indicators of Compromise (IoCs), which identify potential vulnerabilities and attacks
-   Container Threat Detection: it analyzes the behavior of the guest kernel of container to observe any misbehaving
-   VM Threat Detection: detects malicious applications running in your VMs
-   Web Security Scanner: scans over the OWASP Top 10 vulnerabilities over your web facing applications.
#### **Designing secure access to logs**
Use IAM roles
#### **Exporting logs to external security systems**
Sometimes we want to export logs to another location within or outside Google Cloud project. In order to do so, we use what is called _sinks_, which provide a simple way to route log entries to supported destinations. The supported locations are usually Cloud Logging buckets, Cloud Storage, BigQuery, Pub/Sub, and external projects.
#### **Configuring and analyzing Google Cloud Audit Logs and data access logs**
**Cloud Audit Logs**
1.  **Cloud Audit Logs Overview**: Help answer "Who did what, where, and when?" by maintaining four types of audit logs for each Google Cloud project, folder, and organization:

-   **Admin Activity Audit Logs**: Record API calls or administrative actions that modify resource configurations or metadata.
-   **System Event Audit Logs**: Record Google Cloud administrative actions that modify resource configurations, generated by Google systems.
-   **Data Access Audit Logs**: Record API calls that read or modify user-provided resource data, excluding publicly shared resources. **Need the Private Logs Viewer role**
-   **Policy Denied Audit Logs**: Record when access is denied due to security policy violations.

3.  **Viewing Audit Logs**: Requires specific IAM roles (e.g., Logging/Logs Viewer, Project/Viewer)
4.  **Access Transparency Logs**: Provide logs of data access by Google personnel, available to enterprises with appropriate support packages. These logs are surfaced through APIs, Cloud Logging, and Security Command Center.
5.  Enabled by default to BigQuery

**Data Access audit logs**
Can be enabled at various levels (organization, folder, project, resources, billing accounts). The final configuration is a union of all configurations.
6.  **Types of Data Access Audit Logs**:

-   **Admin-Read**: Records operations that read metadata or configuration information.
-   **Data-Read**: Records operations that read user-provided data.
-   **Data-Write**: Records operations that write user-provided data.

3.  **Exemptions**: Specific users or groups can be exempted from having their data accesses recorded to reduce cost and noise.
4.  **Enabling Logs**: Can be done using the Google Cloud CLI or API. Steps include getting current IAM policies, editing the policy file to add auditLogConfigs, and setting the new IAM policy.
#### **Configuring log exports (log sinks and aggregated sinks)**
Log sinks only route log entries from a resource to another supported location. However, we can aggregate logs in aggregated sinks which aggregate logs at folder, organization and resource level. In order to create a sink, you should do it in the Log Router page
#### **Configuring and monitoring Security Command Center**
In Security Command Center, there are two types of services running: built-in services and integrated services. Built-in services are already part of the SCC and integrated services are third party services which can be integrated with SCC.

The Google Cloud Security Command Center (SCC) is a comprehensive security management tool designed to help you discover, mitigate, and prevent attacks on your applications and data. Here's a brief overview of its key features:
1.  **Centralized Dashboard**: Provides a single, centralized view to monitor your cloud assets.
2.  **Consolidated Visibility**: Offers enterprises visibility into their Google Cloud assets across the organization.
3.  **Asset Inventory**: Allows you to see the number of projects, deployed resources, sensitive data locations, and firewall configurations.
4.  **Ongoing Discovery Scans**: Enables viewing of asset history to track changes and unauthorized modifications.
5.  **Anomaly Detection**: Identifies threats like botnets, cryptocurrency mining, anomalous reboots, and suspicious network traffic.
6.  **Event Threat Detection**: Surfaces detected threats for quick corrective action.
7.  **Integration with Security Tools**: Works with Google Cloud security tools like Web Security Scanner and Cloud Data Loss Prevention (Cloud DLP), as well as third-party solutions like Chef, Cloudflare, McAfee, and more.
8.  **Compliance Support**: Uses the Cloud Data Loss Prevention API to scan and report on storage buckets containing vulnerable data, such as PII.
9.  **Real-Time Alerts**: Sends real-time alerts via Pub/Sub to Gmail or SMS for quick action.
10.  **Asset Discovery**: Discovers and indexes assets across your organization, allowing you to view them in one place.
11.  **Historical Scans**: Reviews historical discovery scans to identify new, modified, or deleted assets.
12.  **Third-Party Integration**: Displays findings from third-party security sources registered as Google Cloud Marketplace partners.
## **5. Supporting compliance requirements (~11%)**
### **5.1 Adhering to regulatory and industry standards requirements for the cloud. Considerations include:**
####  **Determining technical needs relative to compute, data, network, and storage**
HIPAA: The minimum requirements for a HIPAA-compliant solution are: Sign a Business Associate Agreement (BAA), enable Cloud Audit Logs, encrypt data at rest with customer-managed keys, and implement least privilege access through Cloud IAM
#### **Evaluating the shared responsibility model**
#### **Configuring security controls within cloud environments to support compliance requirements (e.g., Assured Workloads, organizational policies, Access Transparency, Access Approval, regionalization of data and services)**

-   Assured Workloads: help organizations meet regulatory compliance requirements by enforcing data residency requirements and controlling personnel access based on geographical location.
-   Access Approval: used to require customer approval before Google support can access customer data
-   Organizational policies for compliance: used to provide centralized control over cloud resources and enforce compliance rules
-   Data sovereignty: restrict deployment of new resources to specific regions and limit Google personnel access based on their citizenship or geographic location.

-   Store and manage keys outside Google
-   Grant access to the keys using access justifications
-   Use confidential computing to secure data when used
#### **Determining the Google Cloud environment in scope for regulatory compliance**
#### **Mapping compliance requirements to Google Cloud services and security controls (e.g., network and access segmentation, audit log coverage)**

### **ANNEX:**

**Well Architected Framework Pillars and Perspectives**

1.  Operational excellence: efficiently deploy, operate, manage and monitor workloads
2.  Security, privacy and compliance: maximize security and align with regulatory requirements and standards
3.  Reliability: Resilient and high availability workloads
4.  Cost optimization: maximize business value
5.  Performance optimization: design for optimal performance

Perspective: AI and ML

  

**Envelope encryption**

1.  Generate a DEK (Data Encryption Key) locally.
2.  Use the DEK to encrypt your data. E.g. AES-256
3.  Generate a new key in Cloud KMS or use an existing one, which is the KEK (Key Encryption Key), to encrypt the DEK
4.  Store/send the encrypted data and the encrypted DEK

  

**Best practices and foundations**

-   **Authorization and Authentication:**

-   Use an external Identity Provider as the source of truth
-   Use groups for access control: organize users by job functions or whatever and then assign roles to the group
-   Super admin accounts: enforce 2 step verification with security keys

-   **Network**

-   Use Shared VPC network topology when you do not want direct network connectivity between environments
-   Use hub-and-spoke network topology when you want direct network connectivity between environments

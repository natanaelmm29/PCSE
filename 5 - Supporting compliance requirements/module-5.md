## **5. Supporting compliance requirements (~11%)**
### **5.1 Adhering to regulatory and industry standards requirements for the cloud. Considerations include:**
####  **5.1.1 Determining technical needs relative to compute, data, network, and storage**
HIPAA: The minimum requirements for a HIPAA-compliant solution are: Sign a Business Associate Agreement (BAA), enable Cloud Audit Logs, encrypt data at rest with customer-managed keys, and implement least privilege access through Cloud IAM
#### **5.1.2 Evaluating the shared responsibility model**
#### **5.1.3 Configuring security controls within cloud environments to support compliance requirements (e.g., Assured Workloads, organizational policies, Access Transparency, Access Approval, regionalization of data and services)**

-   Assured Workloads: help organizations meet regulatory compliance requirements by enforcing data residency requirements and controlling personnel access based on geographical location.
-   Access Approval: used to require customer approval before Google support can access customer data
-   Organizational policies for compliance: used to provide centralized control over cloud resources and enforce compliance rules
-   Data sovereignty: restrict deployment of new resources to specific regions and limit Google personnel access based on their citizenship or geographic location.

-   Store and manage keys outside Google
-   Grant access to the keys using access justifications
-   Use confidential computing to secure data when used
#### **5.1.4 Determining the Google Cloud environment in scope for regulatory compliance**
#### **5.1.5 Mapping compliance requirements to Google Cloud services and security controls (e.g., network and access segmentation, audit log coverage)**

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

**Authorization and Authentication:**

-   Use an external Identity Provider as the source of truth
-   Use groups for access control: organize users by job functions or whatever and then assign roles to the group
-   Super admin accounts: enforce 2 step verification with security keys

**Network**
-   Use Shared VPC network topology when you do not want direct network connectivity between environments
-   Use hub-and-spoke network topology when you want direct network connectivity between environments

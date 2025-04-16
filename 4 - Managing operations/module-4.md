## **4. Managing operations (~19%)**
### **4.1 Automating infrastructure and application security. Considerations include:**
#### **4.1.1 Automating security scanning for Common Vulnerabilities and Exposures (CVEs) through a continuous integration and delivery (CI/CD) pipeline**
Through Container Registry vulnerability scanning service, we can integrate security within the CI/CD pipeline lifecycle. This service is continuously scanning vulnerability databases to be up-to-date. The features of this service are:

-   Deep security scans in CI/CD pipeline: it can be integrated with Cloud Build so as soon as an image is created an stored in the Container Registry, a scan is performed
-   Binary Authorization: during deployment, you can ensure that only trusted container images are deployed in GKE or Cloud Run.

#### **4.1.2 Configuring Binary Authorization to secure GKE clusters or Cloud Run**
As we said, binary authorization is a product that enables to implement security measures in the supply chain of the software, when you develop and deploy container-based applications. Binary authorization can:
-   Continuously validation: periodically monitors container images
-   Enforcement: create policies to determine if an image can be deployed or not

It can be used with VPC Service Control to control copying or transferring data (policies, attestors and attestations)

#### **4.1.3 Automating virtual machine and container image creation (e.g., hardening, maintenance, VM patch management)**
Best practices:

1.  Package a single application per container
2.  Properly handle PID 1 and zombie processes: correctly handle signals to stop processes
3.  Optimize for the Docker build cache
4.  Remove unnecessary tools: even using distroless with just the application
5.  Build the smallest image possible
6.  Properly tag images: e.g. versioning
7.  Carefully consider whether to use a public image: maybe they are not good in production, they may be a good point to start from.

**Patch management**: allows to view path status of the fleet of VMs and to automate patching
#### **4.1.4 Managing policy and drift detection at scale (e.g., cloud security posture management, custom organization policies and custom modules for Security Health Analytics)**
-   Cloud Security Posture:
-   Custom organization policies:
-   Security Health Analytics custom modules:
### **4.2 Configuring logging, monitoring, and detection. Considerations include:**
#### **4.2.1 Configuring and analyzing network logs (Cloud Next Generation Firewall [Cloud  NGFW], VPC flow logs, Packet Mirroring, Cloud Intrusion Detection System [Cloud IDS],  Log Analytics)**
-   Firewall Rules logging: enabled per rule, can generate a lot of data, disabled by default and use Log Explorer
-   VPC Flow Logs: record samples of logs for network monitoring
-   Packet mirroring: clones the traffic of instances to another instance for examination. Useful to analyze security status. For example, a security tool could inspect all the mirrored traffic in search for anomalies.
-   Cloud IDS logs: to view audit logs
-   Log Analytics: uses BigQuery analytical power to analyze logs

The workflow works the following:

-   Enable logs: enable the logs that are most relevant for the organization
-   Route logs: route and aggregate logs to the desired destination (BigQuery)
-   Analyze logs: through an analytics tool, analyze the logs
#### **4.2.2 Designing an effective logging strategy**
For production environments logging sampling rate should desirably be 1.0 (100%)
#### **4.2.3 Logging, monitoring, responding to, and remediating security incidents**
Incident Response: it is the process comprehending the actions, escalations, mitigations, resolutions, and notification of any incident that impacts the confidentiality, integrity or availability of data. The process consists of:

-   Identification
-   Coordination
-   Resolution
-   Closure
-   Continuous improvement

**Security Command Center**:
-   Security Health Analytics: detect common vulnerabilities and misconfigurations
-   Attack Path Exposure: shows the path an attacker can follow to exploit a vulnerability
-   Event Threat Detection: produces security findings by analyzing events in Cloud Logging and matching to Indicators of Compromise (IoCs), which identify potential vulnerabilities and attacks
-   Container Threat Detection: it analyzes the behavior of the guest kernel of container to observe any misbehaving
-   VM Threat Detection: detects malicious applications running in your VMs
-   Web Security Scanner: scans over the OWASP Top 10 vulnerabilities over your web facing applications.
#### **4.2.4 Designing secure access to logs**
Use IAM roles
#### **4.2.5 Exporting logs to external security systems**
Sometimes we want to export logs to another location within or outside Google Cloud project. In order to do so, we use what is called _sinks_, which provide a simple way to route log entries to supported destinations. The supported locations are usually Cloud Logging buckets, Cloud Storage, BigQuery, Pub/Sub, and external projects.
#### **4.2.6 Configuring and analyzing Google Cloud Audit Logs and data access logs**
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
Can be enabled at various levels (organization, folder, project, resources, billing accounts). The final configuration is a union of all configurations. Types of Data Access Audit Logs:

-   **Admin-Read**: Records operations that read metadata or configuration information.
-   **Data-Read**: Records operations that read user-provided data.
-   **Data-Write**: Records operations that write user-provided data.

**Exemptions**: Specific users or groups can be exempted from having their data accesses recorded to reduce cost and noise.

**Enabling Logs**: Can be done using the Google Cloud CLI or API. Steps include getting current IAM policies, editing the policy file to add auditLogConfigs, and setting the new IAM policy.
#### **4.2.7 Configuring log exports (log sinks and aggregated sinks)**
Log sinks only route log entries from a resource to another supported location. However, we can aggregate logs in aggregated sinks which aggregate logs at folder, organization and resource level. In order to create a sink, you should do it in the Log Router page
#### **4.2.8 Configuring and monitoring Security Command Center**
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

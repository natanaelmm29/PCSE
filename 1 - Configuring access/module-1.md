# **Exam summary**
## **1. Configuring access (~25%)**
### **1.1 Managing Cloud Identity. Considerations include:**
#### **1.1.1 Configuring Google Cloud Directory Sync and implement single sign-on (SSO) with a third-party identity provider.**

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
#### **1.1.2 Managing a super administrator account**
The super admin account is used to configure the organization resource. It is recommended to keep it isolated from the Organization Administrators, which are not the same. This account has powerful permissions so it should not be used in the daily operations. To secure this account, it is recommended to: enforce 2FA, use a security key or physical authentication. The recommended practice for this type of accounts is to create only two admin accounts and store their credentials in secure locations and not use them unless a break-glass scenario occurs.
#### **1.1.3 Automating the user lifecycle management process**
To use this feature, the app must be configured to use SAML SSO. You also have to have Cloud Identity Premium to configure apps with this feature. If at any time you downgrade, the apps you have configured still function, but you cannot add any other app.
#### **1.1.4 Administering user accounts and groups programmatically**
For this we can use the directory API
#### **1.1.5 Configuring Workforce Identity Federation**
This lets you use an external Identity Provider IdP to authenticate and authorize. You don’t need to synchronize the user identities from the IdP to the Google Cloud identities as it would be with GCloud Directory Sync. We can use this feature with any IdP supporting OpenId Connect OIDC or SAML2.0

For example, the most common scenario is when an Active Directory is already set with all the user accounts. The most important concepts here are:

-   Active Directory serves as the Identity Provider, which means that is in charge of authenticating and authorizing users accessing Google Cloud.
-   When **provisioning** users, they are periodically synchronized (usually with GCDS) to Cloud Identity. It works one way, this means that changes in AD are replicated in GC but not the other way around

You can create workforce identity pools which are set of IdP authenticated identities so that you can group identities by departments, for example, and grant IAM permissions to the pools directly.
### **1.2 Managing service accounts. Considerations include:**
#### **1.2.1 Securing and protecting service accounts (including default service accounts)**
Service accounts use RSA key pairs instead of passwords. The best way to mitigate any threat is to avoid using user managed service account keys and use additional methods to authenticate service accounts.

1.  Avoid User-Managed Keys: Whenever possible, use more secure alternatives to user-managed service account keys to reduce security risks.
2.  Credential Leakage: Protect against credential leakage by ensuring service account keys are not stored in insecure locations.
3.  Privilege Escalation: Prevent unauthorized access and privilege escalation by securing service account keys properly.
4.  Information Disclosure: Avoid disclosing confidential metadata through service account keys.
5.  Non-Repudiation: Ensure that actions performed using service account keys are traceable to prevent bad actors from concealing their identity.
6.  Malicious Credential Configurations: Be cautious of malicious configurations that could bypass security defenses.
#### **1.2.2 Identifying scenarios requiring service accounts**
Service accounts are useful for providing non-human identities to workloads, ensuring secure and consistent access to resources.
#### **1.2.3 Creating, disabling, and authorizing service accounts**
This can be done through the IAM API, GCloud console or the gcloud cli tool.
#### **1.2.4 Securing, auditing, and mitigating the usage of service account keys**
Use whenever possible 
#### **1.2.5 Managing and creating short-lived credentials**
These are short lived credentials used to impersonate a service account. These can be ID tokens to provide identity, or access tokens, to provide the permissions of the service account. These tokens often come without the refresh token, so when the token is expired, the whole process has to be repeated. The tokens can be OIDC ID tokens, self-signed JWT and self-signed blobs
#### **1.2.6 Configuring Workload Identity Federation**
Traditionally, applications running outside Google Cloud can use [service account keys](https://cloud.google.com/iam/docs/service-account-creds#key-types) to access Google Cloud resources. However, service account keys are powerful credentials, and can present a security risk if they are not managed correctly. Workload Identity Federation eliminates the maintenance and security burden associated with service account keys.

With Workload Identity Federation, you can use Identity and Access Management (IAM) to grant to external identities [IAM roles](https://cloud.google.com/iam/docs/overview#roles), direct access on Google Cloud resources. You can also grant access through service account impersonation.

The general steps are: create the workload identity pool, then set the provider for the pool (AWS, etc.), then grant the access to the workload and then configure the workload in the provider side.
####  **1.2.7 Managing service account impersonation**
This is when an authenticated principal, like a user or another service account, authenticates as a service account to gain its permissions. It basically consists of the authenticated principal getting a token of the service account. They are used to temporary grant elevated permissions, test permissions, locally develop apps only working with service accounts and authenticating external apps. It is more secure than using directly the service account key. To manage it, create short-lived credentials.
### **1.3 Managing authentication. Considerations include:**
#### **1.3.1 Creating a password and session management policy for user accounts**
Enforce the use of strong passwords, prevent from using old passwords and explain the importance of strong passwords. Regarding to sessions, enforce frequent reauthentication for privileged users and longer sessions for certain apps
#### **1.3.2 Setting up Security Assertion Markup Language (SAML) and Auth**
#### **1.3.3 Configuring and enforcing 2-step verification**
Set up verification with the password and the phone or a security key
### **1.4 Managing and implementing authorization controls. Considerations include:**
#### **1.4.1 Managing privileged roles and separation of duties with Identity and Access Management (IAM) roles and permissions**
Assign different IAM roles to accounts in separate projects to enforce the principle of least privilege. This ensures that users and service accounts have only the permissions necessary for their tasks. It provides enhanced security, compliance ensuring and management improvements.
#### **1.4.2 Managing IAM and access control list (ACL) permissions**
In Cloud Storage, both methods are used to grant users permissions. The recommended is using IAM but both act in parallel. To only use IAM, you have to set uniform bucket-level access to disable ACLs for all cloud storage buckets. ACLs are usually useful when you want to customize access to individual objects within a bucket
#### **1.4.3 Granting permissions to different types of identities using IAM conditions and IAM deny policies**
We can use IAM conditions to grant access to the principals if only the conditions are met, for example, granting temporal access to fix a problem in the production environment. You can also use IAM conditions in deny policies. For example, you grant an access role to a principal but you set an IAM condition in a deny policy if the resource the principal is accessing is tagged as a prod resource.

Deny policies are used to prevent certain principals from using certain permissions, regardless of the role they have been granted.
#### **1.4.4 Defining access control at the organization, folder, project, and resource level using the principle of least privilege**
Basically know that the permissions are inherited from the parent resource.
#### **1.4.5 Configuring Access Context Manager**
It enables to grant access to principals based on the context of the request, for example, the device type, user identity or even IP address. These are called access levels.
#### **1.4.6 Applying Policy Intelligence**
It helps organizations to control resources and manage access. It helps with:

-   Analyze access: Policy Analyzer helps finding out which principals have access to which resources
-   Troubleshooters to help with access issues
-   Activity Analyzer: when a service account or key was used to call a Google API
-   Service Account Insight: know what SAs have not been used in the past 90 days
-   Role Recommendations: offers recommendations for least privilege responsibilities.
-   Policy Simulator: to prevent any misconfiguration and test policy changes.
#### **1.4.7 Managing permissions through groups**
If there are several principals that need the same set of permissions, it would then be a good a idea to create a group, then to add the principals to the group and finally assign the permissions directly to the group.
#### **1.4.8 Identifying use cases and configuring Privileged Access Manager (PAM)**
It is used to control temporary elevated access to selected principals and view audit logs to determine who got access to what and when. Use cases:

-   Grant emergency access: perform critical tasks without having to wait for approval
-   Control access to critical resources
-   Help secure service accounts: instead of granting roles to SAs whenever they need to perform some special task, allow them to self-elevate when needed
-   Manage access for contractors and externals

### **1.5 Defining the resource hierarchy. Considerations include:**
#### **1.5.1 Managing folders and projects at scale**
#### **1.5.2 Managing pre-built or custom organization policies for the organization, folders, and projects**
Gives centralized and programmatic control over the organization’s cloud resources. While IAM focuses on who can do some tasks, Org Policies focus on what to set the restrictions to, for example, limiting the usage of IAM service accounts. Policies can be set to dry-run mode not to deny requests but to log them to later review. They are used to prevent high-risk configurations.

#### **1.5.3 Using the resource hierarchy for access control and permissions inheritance**
You can set an IAM policy at the [organization level](https://cloud.google.com/resource-manager/docs/access-control-org), the [folder level](https://cloud.google.com/resource-manager/docs/access-control-folders), the [project level](https://cloud.google.com/resource-manager/docs/access-control-proj), or (in some cases) the resource level. Resources inherit the policies of the parent resource. If you set a policy at the organization level, it is inherited by all its child folder and project resources, and if you set a policy at the project level, it is inherited by all its child resources.

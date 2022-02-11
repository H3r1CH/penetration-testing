# PEH

## Active Directory Overview

### Active Directory Overview

#### What is Active Directory?

* Directory service developed by Microsoft to manage Windows domain networks
* Stores information related to objects, such as Computers, Users, Printers, etc.
  * Think about it as a phone book for Windows
* Authenticates using Kerberos tickets
  * Non-Windows devices, such as Linux machines, firewalls, etc. can also authenticate to Active Directory via RADIUS or LDAP

#### Why Active Directory?

* Active Directory is the most commonly used identity management service in the world
  * 95% of Fortune 1000 companies implement the service in their networks \<LINK>
* Can be exploited without ever attacking patchable exploits
  * Instead, we abuse features, trusts, components, and more.

### Physical Active Directory Components

#### Domain Controllers

* A domain controller is a server with the AD DS server role installed that has specifically been promoted to a domain controller. Domain Controllers:
  * Host a copy of the AD DS directory store
  * Provide authentication and authorization services
  * Replicate updates to older domain controllers in the domain and forest
  * Allow administrative access to manage user accounts and network resources

#### AD DS Data Store

* The AD DS data store contains the database files and processes that store and manage directory information for users, services, and applications. The AD DS data store:
  * Consists of the Ntds.dit file
  * Is stored by default in the %SystemRoot%\NTDS folder on all domain controllers
  * Is accessible only through the domain controller processes and protocols

### Logical Active Directory Components

#### AD DS Schema

* The AD DS Schema:
  * Defines every type of object that can be stored in the directory
  * Enforces rules regarding object creation and configuration

#### Domains

* Domains are sued to group and manage objects in an organization. Domains:
  * An administrative boundary for applying policies to groups of objects
  * A replication boundary for replicating data between domain controllers
  * An authentication and authorization boundary that provides a way to limit the scope of access to resources

#### Trees

* A domain tree is a hierarchy of domains in AD DS
  * Ex: contoso.com --> emea.contoso.com, na.contoso.com
* All domains in the tree:
  * Share a contiguous namespace with the parent domain
  * Can have additional child domains
  * By default create a two-way transitive trust with other domains

#### Forest

* A Forest is a collection of one or more domain trees. Forests:
  * Share a common schema
  * Share a common configuration partition
  * Share a common global catalog to enable searching
  * Enable trusts between all domains in the forest
  * Share the Enterprise Admins and Schema Admins groups

#### Organizational Units (OUs)

* OUs are Active Directory containers that can contain users, groups, computers, and other OUs. OUs are used to:
  * Represent your organization hierarchically and logically
  * Manage a collection of objects in a consistent way
  * Delegate permissions to administer groups of objects
  * Apply policies

#### Trusts

* Trusts provide a mechanism for users to gain access to resources in another domain

| Types of Trusts | Description                                                                                   | Diagram |
| --------------- | --------------------------------------------------------------------------------------------- | ------- |
| Directional     | The trust direction flows from trusting domain to the trusted domain                          |         |
| Transitive      | The trust relationship is extended beyond a two-domain trust to include other trusted domains |         |

* All domains in a forest trust all other domains in the forest
* Trusts can extend outside the forest

#### Objects

| Object         | Description                                                                                                          |
| -------------- | -------------------------------------------------------------------------------------------------------------------- |
| User           | <ul><li>Enables network resource access for a user</li></ul>                                                         |
| InetOrgPerson  | <ul><li>Similar to a user account</li><li>Used for compatibility with other directory services</li></ul>             |
| Contacts       | <ul><li>Used primarily to assign e-mail addresses to external users</li><li>Does not enable network access</li></ul> |
| Groups         | <ul><li>Used to simplify the administration of access control</li></ul>                                              |
| Computers      | <ul><li>Enables authentication and auditing of computer access to resources</li></ul>                                |
| Printers       | <ul><li>Used to simplify the process of locating and connecting to printers</li></ul>                                |
| Shared folders | <ul><li>Enables users to search for shared folders based on properties</li></ul>                                     |

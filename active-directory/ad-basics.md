# AD Basics

## Domain Controllers

A domain controller is a Windows server that has Active Directory Domain Services (AD DS) installed and has been promoted to a domain controller in the forest. Domain controllers are the center of Active Directory -- they control the rest of the domain.

* holds the AD DS data store&#x20;
* handles authentication and authorization services&#x20;
* replicate updates from other domain controllers in the forest
* Allows admin access to manage domain resources

## AD DS Data Store

The Active Directory Data Store holds the databases and processes needed to store and manage directory information such as users, groups, and services.

* Contains the NTDS.dit - a database that contains all of the information of an Active Directory domain controller as well as password hashes for domain users
* Stored by default in %SystemRoot%\NTDS
* accessible only by the domain controller

## Forest

﻿A forest is a collection of one or more domain trees inside of an Active Directory network. It is what categorizes the parts of the network as a whole. The Forest consists of:

* Trees - A hierarchy of domains in Active Directory Domain Services
* Domains - Used to group and manage objects&#x20;
* Organizational Units (OUs) - Containers for groups, computers, users, printers and other OUs
* Trusts - Allows users to access resources in other domains
* Objects - users, groups, printers, computers, shares
* Domain Services - DNS Server, LLMNR, IPv6
* Domain Schema - Rules for object creation

## Users + Groups

### Users

There are four main types of users you'll find in an Active Directory network; however, there can be more depending on how a company manages the permissions of its users. The four types of users are:

* Domain Admins - This is the big boss: they control the domains and are the only ones with access to the domain controller.
* Service Accounts (Can be Domain Admins) - These are for the most part never used except for service maintenance, they are required by Windows for services such as SQL to pair a service with a service account
* Local Administrators - These users can make changes to local machines as an administrator and may even be able to control other normal users, but they cannot access the domain controller
* Domain Users - These are your everyday users. They can log in on the machines they have the authorization to access and may have local administrator rights to machines depending on the organization.

### Groups

﻿Groups make it easier to give permissions to users and objects by organizing them into groups with specified permissions. There are two overarching types of Active Directory groups:&#x20;

* Security Groups - These groups are used to specify permissions for a large number of users
* Distribution Groups - These groups are used to specify email distribution lists. As an attacker these groups are less beneficial to us but can still be beneficial in enumeration

## Trusts + Policies

### Trusts

Trusts are a mechanism in place for users in the network to gain access to other resources in the domain. For the most part, trusts outline the way that the domains inside of a forest communicate to each other, in some environments trusts can be extended out to external domains and even forests in some cases.

There are two types of trusts that determine how the domains communicate.

* Directional - The direction of the trust flows from a trusting domain to a trusted domain
* Transitive - The trust relationship expands beyond just two domains to include other trusted domains

### Policies

They dictate how the server operates and what rules it will and will not follow. You can think of domain policies like domain groups, except instead of permissions they contain rules, and instead of only applying to a group of users, the policies apply to a domain as a whole.&#x20;

I'll outline just a few of the  many policies that are default or you can create in an Active Directory environment:&#x20;

* Disable Windows Defender - Disables windows defender across all machine on the domain
* Digitally Sign Communication (Always) - Can disable or enable SMB signing on the domain controller

## AD DS + Authentication

### Domain Services

They are services that the domain controller provides to the rest of the domain or tree. There is a wide range of various services that can be added to a domain controller. Outlined below are the default domain services:

* LDAP - Lightweight Directory Access Protocol; provides communication between applications and directory services
* Certificate Services - allows the domain controller to create, validate, and revoke public key certificates
* DNS, LLMNR, NBT-NS - Domain Name Services for identifying IP hostnames

### Domain Authentication

The most important part of Active Directory -- as well as the most vulnerable part of Active Directory -- is the authentication protocols set in place. There are two main types of authentication in place for Active Directory: NTLM and Kerberos.

* Kerberos - The default authentication service for Active Directory uses ticket-granting tickets and service tickets to authenticate users and give users access to other resources across the domain.
* NTLM - default Windows authentication protocol uses an encrypted challenge/response protocol

## AD in the Cloud

### Azure AD

Azure acts as the middle man between your physical Active Directory and your users' sign on. This allows for a more secure transaction between domains, making a lot of Active Directory attacks ineffective.

### Cloud Security

The best way to show you how the cloud takes security precautions past what is already provided with a physical network is to show you a comparison with a cloud Active Directory environment:

| **Windows Server AD** | **Azure AD**   |
| --------------------- | -------------- |
| LDAP                  | Rest APIs      |
| NTLM                  | OAuth/SAML     |
| Kerberos              | OpenID         |
| OU Tree               | Flat Structure |
| Domains and Forests   | Tenants        |
| Trusts                | Guests         |

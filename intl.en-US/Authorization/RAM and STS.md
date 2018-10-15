# RAM and STS {#concept_27360_zh .concept}

Alibaba Cloud’s permission management function include Resource Access Management \(RAM\) and Security Token Service \(STS\). This function enable users to access Table Store through RAM user accounts with different permissions, and grant users temporary access authorization.

RAM is primarily used to control account system permissions over a long-term period. It allows you to assign different permissions to different RAM users created under your primary account to implement authorization management. For more information, see [RAM](https://partners-intl.aliyun.com/help/doc-detail/28627.htm).

STS is a security credential \(token\) management system that grants temporary access permissions.

## Background {#section_gtm_cyt_cfb .section}

RAM and STS are designed to securely grant access to users without disclosing the primary account’s AccessKey. Unintentional AccessKey disclosure poses serious account security risks as unauthorized users may freely operate the affected primary account, including malicious use of resources and theft of account information.

RAM provides permission control function used to allocate RAM users with different permissions to different entities, minimizing impact to a primary account if a RAM user’s AccessKey is disclosed. Generally, RAM users are created for long-term account operations. Therefore, the AccessKeys of RAM users must not be disclosed.

In contrast to RAM’s long-term control function, STS provides temporary access authorization by returning a temporary AccessKey and token, which can be used directly by temporary users to access Table Store. Generally, the permissions obtained from STS are more restrictive and only valid for a limited period of time.

## Basic concepts { .section}

Basic concepts related to RAM and STS are described as follows:

|Concept|Description|
|:------|:----------|
|RAM user|RAM users are created under an Alibaba Cloud primary account and assigned independent passwords and permissions, with each RAM user having its own AccessKey. RAM users can be used to perform authorized operations in the same way as the primary account. Generally, RAM users can be understood as users with certain permissions or operators with permissions for specified operations.|
|Role|A virtual concept indicating certain operation permissions, roles do not have independent logon passwords or AccessKeys. RAM users can assume roles, and the permissions that are granted when a role is assumed belong to this role. A role may be assumed by multiple users at the same time.|
|Policy|Policies are rules used to define permissions, such as the permissions to read or write certain resources.|
|Resource|Resources are the cloud resources that users can access, such as one or all instances of Table Store, or a certain table in an instance.|

The relationship between a RAM user and its roles is similar to a relationship between an individual and their social identities in different scenarios. For example, a person can assume a role of employee in a company and assume a role of parent at home. Different roles are assigned corresponding permissions. The concept of employee or parent is not an actual entity able to take actions. Roles are complete only when being assumed by RAM users. Furthermore, a role may be assumed by multiple users at the same time. The user who assumes a role is automatically assigned all permissions of the role.

The following example provides more detailed information:

Assume that an Alibaba Cloud primary account named Alice has two Table Store instances named alice\_a and alice\_b. Alice has full permissions on both instances.

To maintain security of the primary account, Alice uses RAM to create two RAM users: Bob and Carol. Bob has the read and write permissions on alice\_a, and Carol has the read and write permissions on alice\_b. Bob and Carol both have their own AccessKeys. If the AccessKey of Bob or Carol is disclosed, only the corresponding instance is affected. Alice can then cancel the permissions of the compromised RAM user through the console.

If Alice needs to authorize another RAM user to read the tables in alice\_a, instead of disclosing Bob’s AccessKey to the user, Alice can create a role \(for example, AliceAReader\), and assign the role the read permission on alice\_a. However, AliceAReader cannot be used directly as no AccessKey corresponds to this role.

To obtain temporary authorization, Alice can call STS’s AssumeRole interface to inform STS that RAM user Bob wants to assume the role AliceAReader. If the interface is successfully called, STS returns a temporary AccessKeyID, AccessKeySecret, and SecurityToken as the access credentials. A temporary user assigned with these credentials obtains the temporary permission to access alice\_a. The credentials’ expiration time is specified when the AssumeRole interface is called.

## RAM and STS best practices { .section}

RAM and STS are designed with complexity to achieve flexible permission control at the cost of simplicity.

RAM users and roles are two concepts used to separate the entity that performs operations from the virtual entity that represents a permission set. If a RAM user requires many permissions \(including read and write permissions\) but each operation only requires part of the total permission set, you can create two roles: one with the read permission and one with the write permission. Then create a user who does not have any permissions but can assume these two roles. When a RAM user needs to read or write data, the RAM user can temporarily assume the role with the required permission. In addition, roles can be used to grant permissions to other Alibaba Cloud users, making collaborations easier while maintaining strict account security.

Implementing RAM or STS through the console and command line operations are strongly recommended to reduce the actual amount of codes that must be used. If code must be used to perform such operations, see the [RAM API Reference](https://partners-intl.aliyun.com/help/doc-detail/28672.htm) and [STS API Reference](https://partners-intl.aliyun.com/help/doc-detail/28756.htm).


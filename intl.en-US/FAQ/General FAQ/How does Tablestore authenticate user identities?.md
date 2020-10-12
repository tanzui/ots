# How does Tablestore authenticate user identities?

Only an AccessKey pair that consists of an AccessKey ID and AccessKey secret can be used to authenticate a user identity. The user can access any resources in your account if they pass the authentication.

An account may have different AccessKey pairs, but Tablestore resources available to the account remain the unchanged regardless of which AccessKey pair is used.

To access Tablestore as a RAM user, the RAM user must be authorized by an Alibaba Cloud account to access specified resources. For more information about RAM users, see [RAM and STS](/intl.en-US/Developer Guide/Authorization management/RAM and STS.md).


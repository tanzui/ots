# Use case {#concept_27361_zh .concept}

This sections explains safe practices when configuring a RAM user for authentication processes. Assume your Alibaba Account has no RAM user, You must replace the default AccessKey with your own AccessKey.

## Create a subaccount { .section}

Assume you have a Table Store instance named ram-test-dev.

In this scenario, we do not recommend that you use the primary account to access an instance so as to avoid potential problems caused by uninitentionally exposing the AccessKey and password.

Procedure

1.  Use the primary account to log on to the [RAM console](https://ram.console.aliyun.com).
2.  In the left-side navigation pane, click **Users**.
3.  Click **Create User** to create a RAM user. Designate it with the same Table Store access permissions as the primary account.
4.  Click **OK**. The AccessKey for the new RAM user ram\_test is generated.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20299/154217630511795_en-US.png)

5.  Save the AccessKey information.
6.  Click **Authorize**to grant the RAM user full access permissions for Table Store.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20299/154217630511796_en-US.png)

7.  \(Optional\) Click **Manage** to grant the account console logon or other permissions.

Example

In this example, the AccessKey is for ram\_test. In actual scenarios, replace it with your own AccessKey.

```
$python ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id VPIzjuDB6T4FGoWM --key r1usnIQ4Tw1yI6bNJkKay6A8EJoMvs

$OTS-TableStoreTest>: ct test pk1:string,pk2:integer readrt:1 writert:1
Table test has been created successfully.

$OTS-TableStoreTest>: dt test
You will delete the table:test!

press Y (confirm) :Y
Table test has been deleted successfully.

```

The ram\_test subaccount can be used for all general operations, so as to avoid exposing the AccessKey of the primary account.

## Read/write permission separation { .section}

To share data of an instance in Table Store without data modification, you can separate read/write permission by creating a subaccount with read-only permission.

Create a RAM user named ram\_test\_pub. Select ReadOnly on the Edit User-Level Authorization page to grant the RAM user read-only access permission for Table Store.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20299/154217630511797_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20299/154217630511798_en-US.png)

Example

Use the AccessKey of the RAM user to test the permissions of creating and deleting a table. In this example, the AccessKey is for ram\_test\_pub. In actual scenarios, replace it with your own AccessKey.

```
$python ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id ftWyMEYu1rBYTbWM --key u4qR5IGu5xJsvSO1y8moyC6n5vA7af

$OTS-TableStoreTest>: ct test pk1:string,pk2:integer readrt:1 writert:1
Fail to create table test.

$OTS-TableStoreTest>: dt test
You will delete the table:test!

press Y (confirm) :Y
Fail to delete table test.

```

**Note:** Due to the read-only access permissions granted to RAM user ram\_test\_pub, it cannot be used to create or delete a table.


# RAM user access {#concept_27363_zh .concept}

To maintain strict account security, use a RAM user to log on to the Table Store console for instance and table management. A RAM user can also be used to call the SDK interface and access data APIs.

## Log on to the console {#section_b3c_zyt_cfb .section}

1.  Log on to the [RAM console](https://partners-intl.console.aliyun.com/#/ram).
2.  Click **Users**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20301/153960227012912_en-US.jpg)

3.  Select the required RAM user \(this example uses ram\_test\), and click **Manage**.
4.  Click **Enable Console Logon** and reset the password.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20301/153960227012925_en-US.png)

5.  Return to the User Management page and click **Dashboard**.
6.  Click the **RAM User Login Link**.
7.  Enter the RAM user’s user name and password set in step 4 to log on to the console.
8.  Click **Table Store** to access the console..

## Enable MFA { .section}

MFA \(Multi-Factor Authentication\) is a simple, effective method that provides additional security protection. When MFA is enabled, a 2-step security verification process is required through use of both a username and password to log on to the Alibaba Cloud console \(first security factor\), and a variable verification code provided by the MFA device \(second security factor\).

1.  Use the primary account to log on to the [RAM console](https://partners-intl.console.aliyun.com/#/ram).
2.  In the left-side navigation pane, click **Users**.
3.  Locate the required RAM user, and click **Manage**.
4.  On the User Details page, click **Enable VMFA Device** and follow the guide to enable the VMFA device.
5.  Enable MFA.

After MFA is enabled, a MFA device verification code is required when logging on to the console.

## Call an API { .section}

Create an AccessKey for the required RAM user and pass in this AccessKey when calling the SDK interface. The RAM user can then operate the same as the primary account.


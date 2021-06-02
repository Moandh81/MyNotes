# Identity and access Management

- IAM = Identity and access management, Global service
- Root account : created by default, shouldn't be used or shared
- Users : people within your organisation and can be grouped
- Groups : contain only users, cannot contain other users
- Users don't have to belong to a group and user
can belong to multiple groups


# IAM permissions

- Users or groups can be assigned json documents called policies

- Theses policies define the **permisions** of the users

- In AWS you apply the **least privilege principle**:
don't give more permissions than a user needs


# IAM Password policy

* Strong Password : high security for your account
* In AWS, you can set a password policy:
    * set a minimum password length
    * require specific characte types
    * allow All IAM users to change their own password
    * Require users to change their password after some time (password expiration)
    * Prevent password reuse


# Multi Factor Authentication

MFA = Password you know + Security Device you own

MFA device options in AWS :
- virtual MFA devices:
  - Google Authenticator
  - Authy


# How can users access AWS account  ?

- **AWS management console** : protected by password and MFA

- **AWS Command line interface** : protected by access key
- **AWS Softwar developer Kit**: for code, protected by access key

Access keys are generated through the AWS console
Users

Access key ID ~= username
Secret Access Key ~= password



# IAM roles for services

Some AWS services will need roles to perform actions 
on your behalf

To do so, we will assign permissions to AWS services
with **IAM roles**

Common roles:

- Ec2 instance roles
- Lambda function roles
- Roles for cloudformation


# IAM Security Tools

- IAM credentials report (**Account level**)
  a report that lists all your account's users and the status of their various credentials

- IAM Access advisor (**User level**)
 
 Access advisor shows the service permissions granted   to a user and when those were last accessed
 You can use this information to revise your policies


 # IAM guidelines and Best Practices

 - Don't use root account except for AWS account setup
 - One phycial user = One AWS user
 - Assign users to groups and assign permissions to groups
 - Create a strong password policy
 - Use and enforce the use of MFA
 - Create and use roles for giving permissions to AWS services
 - Audit permissions with IAM credentials reports
 - Never share IAM users and Access keys
 - Never write IAM credentials in code 


# AWS_Fundamentals-Addressing_Security_Risk
2nd course of the Coursera AWS Fundamentals Specialization
## Shared Responsibilities ##
Security and Compliance is a shared responsibility between AWS and the customer. These are the responsibilities of each one:
* AWS (Security on the Cloud):
  - Operates, manages and controls the components from the host operatinf system.
  - Also the Virtualization layer down to the physical security of the facilities in which the service operates.
  - Protect the infrastruture that runs all of the services offered in the AWS cloud: hardware, software, networking and facilities.
* The customer (Security in the Cloud):
  - Security and management of the guest OS (including updates and patches).
  - Other associated application software.
  - Configuration of the AWS provided security group firewall.
  - It depends on the AWS Cloud services the customer selects from the AWS offering.

<p align="center">
  <img src="https://github.com/robnob/AWS_Fundamentals-Addressing_Security_Risk/blob/main/Shared_Responsibility_Model.JPG" width="700" title="Shared_Responsibility_Model">
 </p>
 
 ## Best Practices ##
 *  Using IAM to crete other user different from root
 *  Principle of least privileges
 *  Principle of minimum needed access policies (Json)
 *  Use of MFA
 *  Strong passwords
 *  Use of roles and groups to assign permissions
 *  Monitoring using Amazon CloudWatch and AWS CloudTrail

##  Create an IAM group for Admin users ##
1.  From the AWS Management Console open the IAM service
2.  Click on User Groups on the left hand menu. Next hit the Create group button
3.  Give a name to the group, for example: AdminGroup. Next scroll down to the Attach permissions policies section and select AdministratorAccess
4.  Click on Create group

## Create an IAM user an attache it to a group ##
1.  From the AWS Management Console open the IAM service
2.  Click on Users on the left hand menu. Next hit the Add Users button
3.  Type down the name, for example, Admin_IT
4.  In the Select AWS Access type section, check both the Programmatic and the AWS Management Console accesses boxes
5.  Leave the defaults: Autogenerated password and Require password reset. Click Next : Permissions
6.  There are three options:
    * Add user to group
    * Copy permissions from existing user
    * Attach existing policies directly
8.  Select the first option and the group created in the previos step. Next: Tags
9.  In the Key type down Department and in the Value, IT. Next : Review
10.  Click on Create user
11.  Download and save the csv (it is not going to be available later) or send an email. Hit the CLose button.
12.  To check the addition of this user to the group: go back to the group menu and see the users under the previously selected group

## AWS Organisations ##

It is possible to:
1.  Automate account creation and management
2.  Create groups of accounts to reflect business needs
3.  Govern Access to AWS services resources region by policies
4.  Set up single payment method for all AWS accounts with consolidated billing
5.  Share resources across acounts

How does AWS Organizations work? First, you will choose an AWS account as a master account. Now, you create an organization in this master account. Note that when you use Organizations to create a new account, an IAM role will be created so that the master account can switch roles to access your member account. Then, you can either create an organization unit called OU (Organizational Units), or accounts, called member accounts, under organization. Then, you can create service control policies to OU or to the member accounts. 

Best Practices:
1. Plan ahead for the structure of your organization. 
2. We recommend that you keep the master account free of any operational AWS resources. 
3. Use AWS CloudTrail in the master account to centrally track all AWS usage in the member accounts.
4. Apply least privilege practice.
5. And for organizational units, assign policies to organizational units rather than to accounts. 
6. Test new and modified policies on a single account before scaling up to OUs.
7. Use the APIs and AWS CloudFormation templates to ensure that every newly graded account is configiured to your liking, this template can create IAM user roles and policies.

## Delegate access across AWS accounts using IAM roles ##

<p align="center">
  <img src="https://github.com/robnob/AWS_Fundamentals-Addressing_Security_Risk/blob/main/Delegate_access_across_AWS_accounts_using_IAM_roles.JPG" width="700" title="Delegate_access_across_AWS_accounts_using_IAM_roles">
 </p>

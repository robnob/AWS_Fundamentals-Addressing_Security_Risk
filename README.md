# AWS_Fundamentals-Addressing_Security_Risk
2nd course of the Coursera AWS Fundamentals Specialization

## Acronyms ##

* VPC = Virtual Private Cloud
* NACL = Network Access Control Lists
* Stateless = If it is allowed in traffic, then it needs to explicitly allowe or deny traffic out
* Statefull =  Return traffic is automatically allowed.
* 
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

## Authentication Vs. Authorization ##
 
Authentication is the process of verifying a user’s identity. Most commonly, users authenticate with a username (which identifies the user) and a password (which confirms the user is who he claims). Authorization, in contrast, is the process of granting users access to specific resources after they have been authenticated. For example, users might be placed into one or more groups based on their job title, and the application then determines which features are available to them based on their group membership.


## Identity Providers ##

An Identity Provider is a service that manages authentication, providing a user login and the ability to verify a user’s identity. AWS Cognito has its own Identity Provider (using User Pools, which are explained below), but it can also integrate with well-established third-party Identity Providers like Facebook and Google. Additionally, Cognito can integrate with any Identity Provider that implements the SAML or OAuth2 protocols. The process of integrating with a third-party for authentication is called Federation.

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

### Step 1: Create a role
First, you use the AWS Management Console to establish trust between the Production account (ID number 999999999999) and the Development account (ID number 111111111111). You start by creating an IAM role named UpdateApp. When you create the role, you define the Development account as a trusted entity and specify a permissions policy that allows trusted users to update the productionapp bucket.

### Step 2: Grant access to the role
In this step of the tutorial, you modify the IAM user group policy so that Testers are denied access to the UpdateApp role. Because Testers have PowerUser access in this scenario, we must explicitly deny the ability to use the role.

### Step 3: Test access by switching roles
Finally, as a Developer, you use the UpdateApp role to update the productionapp bucket in the Production account. You see how to access the role through the AWS console, the AWS CLI, and the API.

Policy to use:

<p>
  {
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
       ],
      "Resource": "arn:aws:s3:::productionapp"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::productionapp/*"
    }
  ]
}
</p>

Policy to apply to the Developer group to access the UpdateApp role previously created:
<p>
  {
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::PRODUCTION-ACCOUNT-ID:role/UpdateApp"
  }
}
</p>

And this is the policy to apply for denying the tester group to assume the UpdateApp role:
<p>
  {
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Deny",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::PRODUCTION-ACCOUNT-ID:role/UpdateApp"
  }
}
  </p>
  
## Identity and Access Services ###

3 User Cases (UK's) for authentication and authorization:

1.  SSO :  Use the corporate identity to access AWS (The corporate AD is federated using Amazon Cognito)
2.  Roles : Grant access to resources without hard coding credentials, nor password, nor API key.
3.  Application access

4 Services related to theses UK's:

1.  AWS Organisations
2.  IAM Manage:
    * users and their access
    * roles and their permissions
    * federated users and their permissions
    * policies:
      - AWS managed
      - Customer managed
      - Inline: it is embbedded into an user, a group or a role.
3.  AWS Single Sign-On (SSO)
4.  Amazon Cloud Directory

## Directory Services ##

A best practice is to logically separate your infrastructure users and end-users. 
Amazon Cognito provides an identity store called Cognito User Pools. This would store all your users so that registration, authentication and even password resets are handled by the user pool.

<p align="center">
  <img src="https://github.com/robnob/AWS_Fundamentals-Addressing_Security_Risk/blob/main/Cognito.JPG" width="700" title="Cognito">
 </p>
 
### User Pools vs. Identity Pools — Understanding the Difference ###

As we mentioned earlier, AWS Cognito is comprised of two separate, but related, services: User Pools and Identity Pools (also called Federated Identities). User Pools provide a user directory for your application, including all the bells and whistles that come with user management, like sign-up, sign-in, group management, etc. User Pools also provide your app with information like the user’s ID and group membership, so that your code can handle authorization. Identity Pools, in contrast, are used to assign IAM roles to users who authenticate through a separate Identity Provider. Because these users are assigned an IAM role, they each have their own set of IAM permissions, allowing them to access AWS resources directly.

Because Identity Pools map a user from an Identity Provider to an IAM role, they essentially allow you to delegate authorization for AWS resources to AWS itself. This is the critical distinction between User Pools and Identity Pools. User Pools (by themselves) don’t deal with permissions at the IAM-level. Rather, they provide information like group membership and the user’s ID to your app, so you can deal with authorization yourself. Identity Pools, in contrast, grant users’ permissions at the IAM level. This means that Identity Pools allow for a much more granular set of permissions, with respect to AWS services.

Let’s use an example to illustrate the distinction. Say you’re developing a serverless app using Cognito and Lambda. If you used User Pools to manage authentication, then you could configure API Gateway to pass through the user’s ID and group membership to your application. This would allow your code to determine if the user has sufficient permissions to access the requested functionality. However, the IAM permissions used to access the underlying AWS resources, like DynamoDB, would come from the Lambda execution role. All users who access your app would be operating under the same IAM role, and it would be up to you to make sure the right users get access to the right resources.

However, if your application was using Identity Pools, then AWS would assign the user to an IAM role, and you could flow the permissions associated with that role through the application. This would mean, for example, that the user could access DynamoDB with her own IAM permissions, rather than the application-wide permissions that come from the Lambda execution role.

Simple AD comes into two sizes:

* small < 500 users
* large < 5000 users

Active Directory connector: re-use an existing on-premises Microsoft AD without caching information in the cloud

## NETWORK ISOLATION ##

Different ways to improve the security of the VPC:

1.  Using NACL's
2.  Using Security Groups which act as firewalls for Amazon EC2
3.  Between VPC's: Private Links
4.  

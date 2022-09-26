# Chapter 6 - AWS Identity & Access Management (IAM)

- **IAM**
  : powerful service that allows you to control how people and programs are allowed to manipulate your AWS infrastructure
  - users, groups and access control policies

- **Principals**
  : an _IAM_ entity that is allowed to interact with AWS resources
  - permanent or temporary
  - represent human or an application
  - types:
    1. Root User
    2. IAM users
    3. Roles / Temporary Security Tokens

- **Root User**
  : first account created with AWS
  - do not use _root_ for everyday administrative tasks

- **IAM Users**
  : persistent entities setup through the _IAM service_ that represent individual people or applications
  - excellent way to enforce the principal of **least priviledge**

- **Principle of Least Priviledge**
  : concept of allowing a person or process interacting with your AWS resources to perform exactly the tasks they need but nothing else

- **Roles/Temporary Security Tokens**
  : used to grant specific privileges to specific actors for a set duration of time
  - _'actors'_ can be authenticated by AWS or some trusted external party
  - provided a temporary security token from the _AWS Security Token Service (STS)_
  - duration of 15min - 36hrs
  - Enable the following use cases:
    - **Amazon EC2 Roles**
      : granting permissions to applications running on an _EC2 instance_
    - **Cross-Account Access**
      : granting permissions to users from other AWS accounts
    - **Federation**
      : Granting permissions to users authenticated by a trusted external system

- **Amazon EC2 Roles**
  : granting permissions to an _application_
  - fixes having to store static credentials in a configuration file
  - When the Amazon EC2 instance is launched, the role is assigned to the instance. When the application running on the instance uses the Application Programming Interface (API) to access the Amazon S3 bucket, it assumes the role assigned to the instance and obtains a temporary token that it sends to the API.

- **Cross-Account Access**
  - grant permissions to users from other AWS accounts
  : recommended as **best practice** as opposed to distributing access keys outside your organisation

- **Federation**
  - _IAM Identity Providers_ providec the ability to federate these outside identities with _IAM_ and assing priviliges to those users authenticated outside of IAM
  - integrate with two different types of _Identity Providers (IdP)_
    - **OpenID Connect (OIDC)**
      : federating web entities
      - e.g facebook, google etc
    - **Security Assertion Markup Language (SAML)**
      : federating internal directories
      - e.g Active Directory or LDAP
  - works by returning a _temporary token_ associated with a role to the _IdP_ for the authenticated identity to use for calls to the _AWS API_.

- **Authentication of a Principal**
  1. User Name/Password
  2. Access Key
     : combination of an access key ID (20 characters) and an access secret key (40 characters)
  3. Access Key/Session Token
     : process operates under an assumed role
     - _Access Key_ also requires a _session token_.

- **Authorization**
  : process of specifying exactly what actions a principal can and cannot perform
  - define specific privileges in _policies_ and associating those policies with principals
 
- **Policies**
  : `JSON` document that fully defined a set of permissions to access and manipulate AWS resources
  - each permission defines:
    - **Effect**
      : `Allow` or `Deny`
    - **Service**
      : for what service does this permission apply?
    - **Resource**
      : specifies the specific _AWS infrastructure_ for which this permission applies
      - specified as an _ARN_
    - **Action**
      : specifies the subset of actions within a service that the permission allows or denies
    - **Condition**
      : optionally defines one or more additional restrictions that limit the actions allowed by the permission

```JSON
{"Version": "2012–10–17", "Statement": [] }
{
  "Sid": "Stmt1441716043000",
  "Effect": "Allow",
  "Action": [
    "s3:GetObject",
    "s3:ListBucket"
  ],
  "Condition": {
    "IpAddress": {
      "aws:SourceIp": "192.168.0.1"
    }
  },
  "Resource": [
    "arn:aws:s3:::my_public_bucket/*"
  ]
}
```

- **Amazon Resource Name (ARN)**
  - format varies slightly between services
  - e.g `arn:awss:service:region:account-id:[resourcetype:]resource`
  - some services _wildcard_ values are allowed

- **Associated Policies with Principals**
  1. **User Policy**
     : exist only in the context of the user to which they are attached
  2. **Managed Policies**
     : exist independently of any individual user
     - same policy can be associated with many users or groups of users
     - large number of predefined managed policies
     - can write your own policies specific to your use case
  3. **Group Policy**
     : exist only in the context of the group to which they are attached
  4. **Assuming a Role**
     : provided with a temporary security token associated with the policies of that role 

- **Multi-Factor Authentication (MFA)**
  : extra layer of security to your infrastructure by adding a second method of authentication beyond just a password or access key

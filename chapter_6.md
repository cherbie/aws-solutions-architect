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


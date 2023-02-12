# aws-journey
Notes that I am preparing while learning AWS





# AWS Fundamentals


## Building Blocks of AWS cloud

  - Regions
     - Distributed geographical locations. Region contains 2 or more availability zones.  
  - Availability Zones
     - Group of one or more data centers (building filled with servers) in a region
  - Edge Locations
     - Content caching locations


## AWS Shared Responsibility Model

  - Customer responsibility "Security in the Cloud"
      - If I can do something through AWS console, it is my i.e. customer responsiblity like patching OS in EC2 instance, customer data, IAM , configuring backup of RDS.
  - AWS responsibility "Security of the Cloud"
      - If I can not control something like hardware upgrade, hypervisor, physical security of data centers etc. is AWS responsibility.
  - Encryption is shared responsibility.


## Main AWS Services

  - Compute
      - EC2, Lambda, Elastic Beanstalk
  - Storage
      - S3, EBS, EFS, FSx, Storage Gateway
  - Databases
      - RDS, DynamoDB, Redshift
  - Networking
      - VPC, Direct connect, Route 53, API gateway, AWS global accelerator


## AWS Well Architected Framework

  - Performance Efficiency
  - Cost optimization
  - Operational Excellence
  - Security
  - Reliability
  - Sustainability

# Identity and Access Management (IAM)

  - IAM is universal and not apply to particular region.

## Identity and Access Management (IAM)

  - Root account is the email address you used to sign up for AWS. 
  - It will have full administrative access of AWS.
  - It must be secured using Multi Factor Authentication (MFA)

## Policy Documents

  - Assign permissions using JSON based policy documents.

## Users & Groups

  - User is a physical person.
  - Users should be grouped in Groups.
  - Best practice - permission should be assign to Groups.
  - A user without any policy attached will not have access to any AWS service.

## Roles

  - Allow one service of AWS to interact with another service of AWS.

## The Principle of Least Privilege

  - Only assign a user the minimum amount of privileges they need to do their job.


# Simple Storage Service S3

  - Object Storage
  - Scalable
  - Unlimited Storage
  - upto 5 TB of files can be stored.
  - File can be of 0 byte also.
  - Store files in buckets (similar to folders)
  - Universal namespace - all S3 bucket name is globally unique.
  - URL to access - https://bucker-name.s3.region.amazonaws.com/file-name-with-extension
  - key-value store - key is file name and value is content of the file.
  - Highly available and highly durable.
  - Access Control Lists (ACLs)
    - ACLs can be attached to individual objects within a bucket to grant or deny access on that particular object.
  - Strong Read-After-Write consistency.
  - Successful file upload through API gives 200 status code.
  - Buckets are private by default.
  - Making bucket content public requies 2 things -
    - Disable Block Public Access.
    - Enable ACL and make public ACL on particular files or add a policy on the bucket which ALLOW read access on all the files in the bucket.


## Static Website on S3

  - Good for hosting static website because it scale on demand automatically.
  - Enable public access on the files for access.


## Versioning Objects in S3

  - All versions are stories in S3 even if you delete an object also.
  - Once enabled, versionining can not be disabled - only suspended.
  - Supports MFA
  - Even though bucket is public but old versions of the files will not become public. They have to made public explicitely.
  - Particular version can be deleted permanently.  
  - When any file is delete, it is not deleted parmanently. A delete marker is added only which can be deleted further to restore the file.



## S3 Storage Classes

### S3 Standard 

 - Data is stored in more than 2 AZs.
 - Default storage class.
 - Designed for frequent access.


### S3 Standard Infrequent Access - S3 Standard-IA

 - less frequently access but requires rapid access like in case of disaster recovery files.
 - per GB retrival fee apply.
 - low per GB stroage price.

### S3 One Zone Infrequent Access

 - Like S3 Standard-IA but data is stored redundantly within a single AZ.
 - Good for non-critical data.

### S3 Intelligent-Tiering

 - Automatically moves data to the most cost-effective tier based on access frequency.
 - Use AI to determine this.

## Glacier

 - Pay each time for access.
 - Objects are stored in Vaults similar to bucket in S3.
 - Used for long time data archiving.

### Glacier Instant Retrival

### Glacier Flexible Retrival

  - Flexibiity to retrive large set of data at no cost.
  - Retrival take from minutes up to 12 hours.

### Glacier Deep Archive

  - Most cheapest storage class.
  - Use for storing data for 7-10 years.
  - Retrival takes 12 and bulk retrival can take up to 48 hours.


## Lifecycle Management

  - Automates moving objects between different tiers, thereby maximizing cost effectiveness.
  - Can be used along with versioning to move older versions to different tiers.

 ## S3 Object Lock

   - Use s3 Object Lock to store objects versions using a Write once and read many (WORM) model.
   - Objects versions can be locked for a perdefined period of time called Retention period.
   - Object lock works only in versioned buckets.
   - Placing an object lock on an object protects only the version specified in the request. it doesn't prevent new versions of the object from being created.
   - Object lock can be on individual objects or can be applied on whole bucket.
   - There are two modes of S3 object lock.

### Governance Mode

   - With governance mode, objects versions can be modified or deleted if the person has some special permissions. Retention settings can also be changed using special permissions.

### Compliance Mode
   
   - With Compliance mode, objects versions can not be modified or deleted even by the root user itself. Retention period can't be changed.

### Legal Holds

  - S3 Object Lock which also prevents an object version from being overwritten or deleted.
  - With legal holds, there is no retention period. So it remains effective until removed.
  - With legal holds, S3 objects modification or deletion can be prevented even when retention period is expired.
  - `s3:PutObjectLegalHold` permission is required for adding or removing legal holds.


## Glacier Vault Lock

  - Allows to enforce compliance controls for individual S3 Glacier vaults with a vault lock policy.
  - Controls like WORM can be specified in vault lock policy and lock the policy from future edits. Once locked, the policy can no longer be changed.


## Encrypting S3 Objects

### Encryption in Transit

  - Uses SSL/TLS certificate and https

### Encryption at Rest - Server Side Encryption (SSE)
  
  - 3 ways to do encryption -
      - Using SSE-S3 - Uses S3-managed keys, using AES 256-bit encryption. 
      - Using SSE-KMS - Uses AWS-KMS managed keys.
      - SSE-C - Uses customer-provided keys.


### Encryption at Rest - Client-side encryption
  - Objects are encrypted by client before uploading them to S3.


### Enforce Encryption 

#### Using Console

   - Enable ecryption at AWS console for whole bucket.

#### Using Bucket Policy

   - While uploading a file in S3, request can contain http request header `x-amz-server-side-encryption` with value `AES256` for SSE-S3 or with value `aws:kms` for SSE-KMS. This header indicates that file should be encrypted at upload time using specified SSE keys.
   - Bucket policy can be created for denying request which does not have this header present to enfore encryption at upload time. 

## S3 Performance Optimization

### S3 Prefixes

   - S3 prefix is just a folder inside a bucket which contains files.

### S3 Performance

   - S3 supports 5500 GET/HEAD request per second per prefix.
   - SSE-KMS can be a limitation for S3 performance if it is used for encryption.
   - **Multipart upload** can be used for parallelize uploads. It is recommended for files over 100 MB and requires for files over 5 GB
   - **S3 Byte-Range Fetches** parallelize downloads by specifying byte ranges.

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
  - Role is intended to be assumable by anyone who needs it.
  - Assuming role is temporary. When you assume a role, it provides you with a temporary security credentials for your role session.
  - Roles can be assumed by people, AWS architecture or other system-level accounts.
  - Roles can allow cross-account access. This allows one AWS account the ability to interact with resources in other AWS accounts.
  - Roles can be attached or detached to running EC2 instances without stopping them.

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

   - S3 prefix is just a folder or subfolder inside a bucket which contains files.

### S3 Performance

   - S3 supports 5500 GET/HEAD request per second per prefix.
   - SSE-KMS can be a limitation for S3 performance if it is used for encryption.
   - **Multipart upload** can be used for parallelize uploads. It is recommended for files over 100 MB and requires for files over 5 GB
   - **S3 Byte-Range Fetches** parallelize downloads by specifying byte ranges.


## S3 Replication and backup

   - Objects can be replicated from one bucket to another, from one account to another or from one region to another.
   - Versioning must be enabled for replication in source and destination bucket.
   - Objects in existing buckets are not replicated automatically. Once replication is on, all subsequent updated objects will be replicate automatically.
   - Delete markers are not replicated by default but can be turned on.

## S3 Batch Operations

   - S3 batch operations is used to perform operations in all objects or a subset of objects in the bucket.
   - With batch operation, we can -
      - Modify object metadata and properties.
      - Copy objects betweeen buckets.
      - Invoke AWS lambda functions for each object.
   - S3 Batch Replication operation provides way to replicate objects that existed before a replication configuration was in place.


# Elastic Compute Cloud EC2

## EC2 Pricing Options

### On-Demand
  
  - Pay by minute or second depend on the instance type.
  - No commitment of usage.
  - No upfront payment.

### Reserved
   
   - Reserve instances for 1-3 years. 
   - Good for predictable usage.
   - Payment can be done full upfront, partial upfront or none.
   - Full upfront will be the cheapest option. 
   - Reserved instances belongs to only one region.
   - Can be counted against serverless technologies like Lambda and Fargate.

### Standard Reserved Instances

   - Upto 72% discount off the on-demand price.

### Convertible Reserved Instances

   - Up to 54% off the on-demand price.
   - Has the option to change to a different RI type of equal or greater value.

### Scheduled Reserved Instances

   - Lauch within the time window you define.
   - Time window is fixed and recurring that only requires a fraction of a day, week or month usage.

### Spot

   - Purchase unused capacity on discount of up to 90% of on-demand price.
   - Prices fluctuate with supply and demand.
   - Good for stateless, fault-tolerant or flexible applications.
   - If the spot price goes above your maximum purchase price, spot instances either stop or terminate depending on the choice in 2 mins.

#### Spot Block

   - Spot block can be used to stop your Spot instances from being terminated even if the spot price goes above maximum purchase price.
   - Spot block can be set between 1 to 6 hours.

#### Spot Fleets

   - A collection of Spot instances and optionally on-demand instances.
   - Spot fleet will try and match the target capacity with your price restraints.
   - The Spot Fleet selects the Spot capacity pools that meet your needs and launches Spot Instances to meet the target capacity for the fleet. 
   - A Spot capacity pool is a set of unused EC2 instances with the same instance type (for example, m5.large), operating system, Availability Zone, and network platform. When you make a Spot Fleet request, you can include multiple launch specifications, that vary by instance type, AMI, Availability Zone, or subnet. The Spot Fleet selects the Spot capacity pools that are used to fulfill the request, based on the launch specifications included in your Spot Fleet request, and the configuration of the Spot Fleet request. The Spot Instances come from the selected pools.
    - The request for Spot Instances is fulfilled if there is available capacity and the maximum price you specified in the request exceeds the current Spot price.
   - By default, Spot Fleets are set to maintain target capacity by launching replacement instances after Spot Instances in the fleet are terminated. 
   - You can submit a Spot Fleet as a one-time request, which does not persist after the instances have been terminated. You can include On-Demand Instance requests in a Spot Fleet request.
   - Your launch configuration determines all the possible Spot capacity pools (instance types and Availability Zones) from which Spot Fleet can launch Spot Instances. However, when launching instances, Spot Fleet uses the allocation strategy that you specify to pick the specific pools from all your possible pools.
   - There are 4 strategies which can be defined -
      - <b>Capacity Optimized</b> - Spot instances come from the pool with optimal capacity for the number of instances launching. This strategy has the lowest risk of interruption.
      - <b>Diversified</b> - Spot instances are distributed across all pools.
      - <b>Lowest Price</b> - Spot instances come from the pool with the lowest price. This is default strategy. This strategy has the highest risk of interruption.
      - <b>Price capacity optimized</b> It is recommended strategy. Spot Fleet identifies the pools with the highest capacity availability for the number of instances that are launching. This means that AWS will request Spot Instances from the pools that they believe have the lowest chance of interruption in the near term. Spot Fleet then requests Spot Instances from the lowest priced of these pools. The priceCapacityOptimized allocation strategy is the best choice for most Spot workloads, such as stateless containerized applications, microservices, web applications, data and analytics jobs, and batch processing.
      - <b>instancesPoolsToUseCount</b> - The number of spot pools acroos which to allocate your target spot capacity. This parameter is valid only when used in combination with lowestPrice. Spot Fleet selects the lowest priced Spot pools and evenly allocates your target Spot capacity across the number of Spot pools that you specify.

### Dedicated

   - A physical EC2 server dedicated for use.
   - The most expensive option.
   - Should be used when you have -
      - Regulatory requirements that may not support multi-tenant virtualization.
      - Licensing which are not portable or does not support multi-tenancy or cloud deployments.
   - Can be puchased on-demand (hourly)
   - Can be reserved and get upto 70% discount off the on-demand price.


## AWS Pricing Calculator - calculator.aws

   - Estimate the cost for moving into cloud.
   - Estimates can be made including EC2, database etc. 

## Connect to EC2 instances

### EC2 Instance Connect
   
   - EC2 instance connect allows for a convenient and secure native SSH connection using short-lived keys.
   - It requires the host security group to permist ssh traffic inbound.
   - It supports only Linux EC2 hosts

###  Session Manager

   - Session manager permits SSH connection tunneled over a proxy connection.
   - Session manager supports Windows and Linux hosts and both EC2 instances and On-prem.

###  SSH Client

   - Connect from your machine using generated keys.

## Security Groups

  - Security groups are virtual firewalls for EC2 instances.
  - By default, all inbound traffic is blocked and all outbound traffic is allowed.
  - Changes to security groups take effect immediately.


## Bootstrap Scripts

  - A script that runs when the instance first runs.
  - It runs at root level.
  - Writing large script adds to the amount of time it takes to boot the instance.
  - Useful for automating the installation of applications.
  - It is also called user data.

## EC2 Metadata

  - Data about EC2 instances like placement groups, local or public IP, security groups etc.
  - Can be accessed inside EC2 instance by curl.
  - Use `curl http://169.254.169.254/latest/meta-data/` to retrive all categories of data.
  - Use `curl http://169.254.169.254/latest/meta-data/<category>` to retrive data of a particular category.


## EC2 Networking

  - Multiple network interface can be attached to EC2 instance.
  - Primany network interface can not be detached from EC instance.
  - 3 differet types of virtual networking cards can be attached to EC2 instances -

### Elastic Network Interface ENI

  - For basis, day-to-day networking.
  - Low budget, high availability solution.
  - Attached by default to EC2 instances.
  - It allows -
     - Private IPv4 addresses
     - Public IPv4 address
     - Many IPv6 addresses
     - MAC address
     - 1 or more security groups.
  - Use cases -
     - Create a management network or logging network etc with different IP address and differnet subnets in a single AZ.
     - Use network and security appliances with mutiple ENI configuration eg load balancer, NAT, proxy.
     - EC2 instance can have multiple ENIs in different subnets in the same AZ. Such instances are called dual-homed instance. So, ENI can be used to create dual-homed instances with workloads/roles on distinct subnets.

### Enhanced Networking EN

  - Uses <b>Single Root I/O Virtualization (SR-IOV)</b> to provide high performance and lower CPU utilization.
  - SR-IOV allows VMs to bypass hypervisor and talk to hardware directly.

![SR IOV functions](images/ec2-en-sr-iov.PNG?raw=true "SR IOV functions")

  - High performance networking between 10Gbps - 100 Gbps.
  - Provides higher bandwidth, higher packet per second (PPS) performance and consistently lower inter-instance latencies.

  - No additional charge.
  - All current generation instance types support EN except for T2 instances.
  - Can be enabled using 2 ways -

#### Elastic Network Adaper (ENA)

  - Supports network speeds of up to 100 Gbps for supported instance types.  

#### Intel 82599 Virtual Function (VF) interface

  - Supports network speed of up to 10 Gbps for supported instance types. 
  - Generally used on older instances.

### Elastic Fabric Adapter EFA

  - Accelerates High Performance Computing (HPC) and machine learning applications.
  - Providees lower and more consistent latency and higher throughput than the TCP transport traditionally used in cloud-based HPC systems.
  - EFA is an ENA with an additional <b>OS-BYPASS</b> functionality.
  - OS-bypass enables HPC and machine learning applications to bypass OS kernal and communicated directly with the EFA device. 
  - It makes it a lot faster with much lower latency.
  - It is only supported with Linux, not with Windows.


## EC2 Placement Groups

  - Placement groups can'b be merged.
  - Existing instances can be moved into a placement group but the instance should be in stopped state. Movement can be done through AWS CLI or SDK only.

### Cluster Placement Group

  - Group EC2 instances in a single AZ.
  - Result in Low network latency and high network throughput.
  - AWS recommends homogeneous instances within cluster placement group.

### Spread Placement Group
  
  - Individual EC2 instances are placed on different hardware.
  - Good for critical EC2 instances so that hardware failure of one machine does not stop whole system.
  - Use for individual EC2 instances.
  - Max 7 instances per group per AZ.
  - Can span to multiple AZ.

### Partition Placement Group

  - EC2 instances are grouped and placed on different partitions called Racks.
  - Use for multiple EC2 instances.
  - Can span to multiple AZ.
  - Max 7 partitions in partition group.
  - Amazon EC2 ensures that each partition within a placement group has its own set of racks. Each rack has its own network and power source. No two partitions within a placement group share the same racks, allowing you to isolate the impact of a hardware failure within your application.


## Deploying vCenter in AWS with VMware Cloud on AWS

  - VCenter can be deployed on AWS using VMWare and it is ordered through VMWare themselves.
  - VMware is used by organizations around the world for private cloud deployments.
  - Some organizations opt for a hyrid cloud strategy and wants to connect on-premises cloud to AWS public cloud.
  - Organizations would be migrating to cloud may also be using VMware's built-in tools.
  - It can also be used for Disaster Recovery using hybrid cloud model.
  - vCenter runs on dedicated hardware hosted in AWS using a single AWS account.
  - Each host is capable for running multiple VMware instances (up to 100)
  - Cluster can start with 2 hosts up to a maximum of 16 hosts per cluster. These cluster are vCenter cluster.

## AWS Outposts

  - Outposts bring AWS data center directly to you, on-premises. It extends AWS to your data center.
  - Outposts allow to have large variety of AWS services in your data center.
  - Allows to create a hybrid cloud.
  - Fully managed infrastructure You do not need a dedicated team to look after your outposts infrastructure.
  - Bring the AWS management console, APIs and SDKs into your data center, allowing uniform consistency in your hybrid environment.

## AWS Outposts Family Members

### Outposts Rack

  - Available starting with a single 42U rack and scale up to 96 racks.
  - Gives the same AWS infrastructure, services and APIs in your own data center.
  - It is for large deployments.

### Outposts Servers

  - Individual Servers in 1U or 2U form factor.
  - Useful for small space requirements like retail stores, brnach offices etc.
  - Provides local compute and networking services.
  - It is for smaller deployments.


# Elastic Block Store EBS

  - Storage volumes you can attach to your EC2 instances.
  - Designed for mission critical data
     - Production workloads.
  - Highly available
     - Automatically replicated within a single AZ to protect against hardware failures.
  - Scalable
     - Dynamically increase capacity and change the volume type with no downtime or performance impact to your live systems.

## EBS Volumns types

### EBS Volumns types - Solid State Disk SSD 

### General Purpose SSD

   - A balance of price and performance.

#### gp2

   - Good for boot volumes or development and test applications that are not latency sensitive.

#### gp3

   - New generation.
   - Good for applications that require high performance at a low cost like MySQL, Cassandra, virtual desktops and Hadoop analytics.
   - The top performance of gp3 is 4 times faster than max throughtput of gp2 volumes.
   - Provides IOPS up to 16000

### Provisioned IOPS (PIOS) SSD

#### io1

   - The high performance option and the most expensive.
   - Up to 64000 IOPS per volume.
   - Designed for I/O intensive applications like large databases and latency-sensitive workloads.

#### io2

   - Latest generation
   - io2 is the same price as io1
   - Higher durability and more IOPS.
   - Good for applications that need high level of durability.


![EBS SSD Volumes](images/ebs-ssd-volumes.PNG?raw=true "EBS SSD Volumes")


### EBS Volumns types - Hard Disk Drive HDD

   - Magnetic drives.
   - Can not be boot volume.

#### Throughput Optimized HDD (st1)

   - Low-cost HDD volume.
   - Frenquently accessed, throughput-intensive workloads like Big data, data warehouses, ETL and log processing.


#### Cold HDD (sc1)

   - Lowest cost option.
   - Good choice for colder data requiring fewer scans per day.
   - Good for applications that need the lowest cost and performance is not a factor like file server.
    

### IOPS

  - Measures the number of read and write operations per second.
  - Important metric for quick transactions, low-latency apps, transactional workloads.
  - The ability to action read and write very quickly.

 ### Throughtput

  - Measures the number of bits read or written per second (MB/s)
  - Important metric for large datasets, large I/O sizes, complex queries.
  - The ability to deal with large datasets.


### Volumns

 - Volumes are simply virtual hard disks.
 - Volumes exist on EBS.
 - Minimum 1 volume is required per EC2 instance which is called root device volume.
 - EBS volumes will always be in the same AZ as EC2.
 - EBS volumes can be resize on the fly without restarting EC2 instance. However, file system need to be extended in the OS so tha OS can see the resized volume.
 - Volume type can also be changed on the fly.



### Snapshots

  - Snapshots exist on S3
  - Point-in-time copy of a volume.
  - Snapshots are incremental. Only the data that has been changed since your last snapshot are moved to S3.
  - First snapshot takes longer as it has to create full copy of the volume.
  - For consistent snapshots, it is recommended to stop the instance and take a snap.
  - Encrypted EBS volume creates encrypted snapshot automatically.
  - Snapshots can be shared but only in the region in which they were created. To share to other regions, you will need to copy them to the destination region first.


### Copy EC2 instance from one region to another

   - Run EC2 instance in one region.
   - Create snapshot of attached EBS volume.
   - Copy snapshot to another region.
   - Create AMI from the copied snapshot.
   - Use this AMI for spinning up EC2 instance.

### EBS Encryption

   - EBS encrypts your volume with a data key using AES-256 algorithm.
   - Amazon EBS encryption uses AWS Key management service (KMS) or customer master keys (CMK) when creating encrypted volumes and snapshots.
   - Once encryption is enabled -
      - Data at rest is encrypted inside the volume.
      - All data in flight moving between the instance and the volume is encrypted.
      - All snapshots are encrypted.
      - All volumes created from snapshot are encrypted.
   - Unencrypted snapshots can be encrypted during copy.
   - Root device volumes can be encrypted upon creation.

### EC2 hibernation

  - Preserves in-memory RAM on persistent storage (EBS)
  - Instances can't be hibernated for more than 60 days.
  - Available for On-demand and Reserved instances.

## Elastic File System EFS

  - Managed Network File System (NFS) that can be mounted on many EC2 instances at the same time.
  - EFS works with EC2 instances in multiple AZ.
  - Highly available and scalable but expensive.
  - Uses NFSv4 protocol.
  - Compatible with Linux based AMI.
  - Encryption at rest using KMS
  - File system scales automatically, no capacity planning required.
  - Pay per use.
  - Performance is very good and can be scaled up to petabytes.
  - Supports one zone only as well as multiple AZs.
  - Read after write consistency.
  - When creating EFS file system, performance characteristics can be set -
      - **General Purpose** - used for things like web servers, CMS etc.
      - **Max I/O** - used for big data, media processing etc.


### EFS Stroage Tiers

  - Comes with 2 different tiers and support lifecycle management.

#### Standard 
  - For frequently accessed files.

#### Infrequently Accessed
  - For files not frequently accessed.


## FSx for Windows

  - Amazon FSx for Windows File Server provies a fully managed native Microsoft Windows file system.
  - A managed windows server than runs **Windows Server Message Block (SMB)** - based file services.
  - Supports AD users, access control lists, groups and security policies along with Distributed File System (DFS) namespaces and replication.

## FSx for Lustre

  - A fully managed file sytem that is optimized for compute-intensive workloads.
  - Good for anything related to AI and ML like -
      - High Performance Computing
      - Machine Learning
      - Media Data Processing Workflows
      - Electronic Design Automation
  - With FSx, Lustre file system can be launched that can process massive datasets at up to hundreds of gigabytes per second of throughout, millions of IOPS and sub-millisecond latencies.
  - It can store data directly on S3.
  - Compatible wit all linux.

## Amazon Machine Image AMI

  - All AMIs are categorized as either backed by -

### Amazon EBS

   - Root device for an instance launched from AMI is an Amazon EBS volume created from an Amazon EBS snapshot.

### Instance Store

   - Root device or an instance launched from AMI is an instance store volume created from a template stored in Amazon S3.


### Instance Store Volumes

   - Ephemeral storage.
   - Cannot be stopped.
   - Reboot of the instance is possible.
   - Once instance is deleted, instance store volume is lost.
   - Instance backed up instance store can not be stopped.
   - If underlying hardware fails, data is lost.


## AWS Backup

   - It allows to consolidate backups across multiple AWS services.
   - Can be used with AWS orgnanisation which gives centralized control across all AWS services, in multiple AWS accounts across the entire AWS organization.
   - Automated backup schedules and retention policies can be created. 
   - Lifecycle policies can also be created which allows to expire unnecessary backups after a perior of time.
   - It improves compilance. Backup policies can be enforced while backups can be encrypted both at rest and in transit, allowing alignment to regulatory compliance.
   - Auditing also become easy due to a consolicated view of backups.

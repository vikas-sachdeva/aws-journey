# aws-journey
Notes that I am preparing while learning AWS


# Building Blocks of AWS cloud

  - Regions
     - Distributed geographical locations  
  - Availability Zones
     - Group of one or more data centers in a region
  - Edge Locations
     - Content caching locations


# AWS Shared Responsibility Model

  - Customer responsibility "Security in the Cloud"
      - If I can do something through AWS console, it is my i.e. customer responsiblity like patching OS in EC2 instance, configuring backup of RDS.
  - AWS responsibility "Security of the Cloud"
      - If I can not control something like hardware upgrade, physical security of data centers etc. is AWS responsibility.


# Main AWS Services

  - Compute
      - EC2, Lambda, Elastic Beanstalk
  - Storage
      - S3, EBS, EFS, FSx, Storage Gateway
  - Databases
      - RDS, DynamoDB, Redshift
  - Networking
      - VPC, Direct connect, Route 53, API gateway, AWS global accelerator


# AWS Well Architected Framework

  - Performance Efficiency
  - Cost optimization
  - Operational Excellence
  - Security
  - Reliability
  - Sustainability


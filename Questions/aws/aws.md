# AWS Interview Notes (2–3 Years Experience)

## 1. What is AWS?

### Expected Answer

AWS (Amazon Web Services) is a cloud computing platform provided by Amazon that offers on-demand services such as compute power, storage, databases, networking, security, and analytics over the internet.

### Why is it used?

Instead of purchasing and maintaining physical servers, organizations can rent resources from AWS and pay only for what they use.

### Real-Time Example

If an e-commerce application needs 10 servers during a sale and only 2 servers afterward, AWS allows scaling resources up and down without buying hardware.

### Interview Follow-up

Q: What are the benefits of AWS?

* Pay-as-you-go pricing
* Scalability
* High availability
* Security
* Global infrastructure

---

## 2. What is Cloud Computing?

### Expected Answer

Cloud computing is the delivery of computing resources such as servers, storage, databases, networking, and software over the internet on a pay-as-you-use basis.

### Why is it used?

It eliminates the need to own and maintain physical infrastructure.

### Real-Time Example

Instead of storing files on a local hard drive, companies store them in cloud storage and access them from anywhere.

### Key Benefits

* Cost savings
* Flexibility
* Scalability
* Global access

---

## 3. What is EC2?

### Expected Answer

EC2 (Elastic Compute Cloud) is a service that provides virtual servers in AWS. These virtual servers are called Instances.

### Why is it used?

To host applications, websites, APIs, and backend services.

### Real-Time Example

A Spring Boot application can be deployed on an EC2 instance and accessed by users over the internet.

### Interview Follow-up

Q: Why is it called Elastic?
Because resources can be scaled up or down based on demand.

---

## 4. What are the EC2 Pricing Models?

### 1. On-Demand

Pay only when you use the instance.

Use Case:
Testing environments and short-term projects.

### 2. Reserved Instance

Reserve capacity for 1 or 3 years and get discounts.

Use Case:
Production applications.

### 3. Spot Instance

Use unused AWS capacity at a discounted price.

Use Case:
Batch processing and testing.

Limitation:
AWS can terminate the instance anytime.

### 4. Dedicated Host

A complete physical server dedicated to one customer.

Use Case:
Compliance and licensing requirements.

---

## 5. What is an AMI?

### Expected Answer

AMI (Amazon Machine Image) is a template used to create EC2 instances.

It contains:

* Operating System
* Installed Software
* Configurations
* Application Setup

### Real-Time Example

If you have configured one EC2 instance completely, you can create an AMI and launch multiple identical instances.

### Follow-up

Q: Can AMIs be shared?
Yes, with specific AWS accounts.

Q: Are AMIs created automatically?
No, they must be created manually.

---

## 6. What is Elastic IP?

### Expected Answer

Elastic IP is a static public IPv4 address provided by AWS.

### Why is it needed?

Normal public IP addresses change when an instance is stopped and started.

Elastic IP remains constant.

### Real-Time Example

Production websites often use Elastic IPs to avoid changing DNS records.

### Important Point

Default limit:
5 Elastic IPs per AWS account per region.

---

## 7. Difference Between Stopping and Terminating an EC2 Instance

### Stopping

* Instance shuts down.
* Data on EBS remains.
* Instance can be restarted.

### Terminating

* Instance is permanently deleted.
* Cannot be restarted.
* Associated resources may also be removed.

### Interview One-Liner

Stopping is temporary; terminating is permanent.

---

## 8. What are Key Pairs?

### Expected Answer

Key Pairs are used to securely connect to EC2 instances.

They consist of:

* Public Key (stored in AWS)
* Private Key (stored on your machine)

### Real-Time Example

Using SSH to connect to a Linux EC2 instance.

### Important Point

Without the private key, you cannot access the instance.

---

## 9. What is Amazon S3?

### Expected Answer

Amazon S3 (Simple Storage Service) is an object storage service used to store and retrieve files over the internet.

### What can be stored?

* Images
* Videos
* Documents
* Logs
* Backups

### Real-Time Example

Application images and user-uploaded files are commonly stored in S3.

### Benefits

* Highly durable
* Scalable
* Secure
* Cost-effective

---

## 10. What are Buckets, Objects, and Keys in S3?

### Bucket

Container that stores files.

### Object

Actual file stored in S3.

### Key

Unique identifier of an object.

Example:

Bucket:
employee-documents

Object:
resume.pdf

Key:
resume.pdf

---

## 11. What are the Use Cases of S3?

* Backup and Recovery
* Static Website Hosting
* Log Storage
* Media Storage
* Data Analytics
* Application File Storage

### Real-Time Example

Store application logs in S3 for troubleshooting.

---

## 12. What are S3 Storage Classes?

### S3 Standard

Frequently accessed data.

### S3 Standard-IA

Infrequently accessed data.

### S3 One Zone-IA

Stored in one availability zone.

### S3 Glacier

Archival storage.

### S3 Glacier Deep Archive

Lowest-cost long-term storage.

### Interview Tip

Frequently used → Standard

Rarely used → Glacier

Very rarely used → Deep Archive

---

## 13. What is Amazon RDS?

### Expected Answer

RDS (Relational Database Service) is a managed database service provided by AWS.

### Supported Databases

* MySQL
* PostgreSQL
* Oracle
* SQL Server

### Why is it used?

AWS handles:

* Backups
* Patching
* Maintenance
* High Availability

### Real-Time Example

Spring Boot applications commonly connect to MySQL hosted on RDS.

---

## 14. What is VPC?

### Expected Answer

VPC (Virtual Private Cloud) is a logically isolated network in AWS where resources can be deployed securely.

### Why is it used?

Provides security and control over networking.

### Components

* Subnets
* Route Tables
* Internet Gateway
* Security Groups

### Real-Time Example

Deploy application servers in private subnets and expose only load balancers to the internet.

---

## 15. What is IAM?

### Expected Answer

IAM (Identity and Access Management) is used to manage users, groups, roles, and permissions in AWS.

### Why is it used?

To control who can access which AWS resources.

### Real-Time Example

Developers may get EC2 access while administrators get full AWS access.

### Key Components

* Users
* Groups
* Roles
* Policies

---

## 16. What is AWS Lambda?

### Expected Answer

AWS Lambda is a serverless compute service that allows running code without managing servers.

### Benefits

* No server management
* Auto scaling
* Pay per execution

### Real-Time Example

Process uploaded files automatically when they arrive in an S3 bucket.

---

## 17. What is SNS?

### Expected Answer

SNS (Simple Notification Service) is a messaging service used to send notifications.

### Supports

* Email
* SMS
* Push Notifications

### Real-Time Example

Send order confirmation notifications after successful purchases.

---

## 18. What is CloudWatch?

### Expected Answer

CloudWatch is AWS's monitoring and observability service.

### What does it monitor?

* EC2
* Lambda
* RDS
* Application Logs
* Metrics

### Real-Time Example

Send alerts when CPU utilization exceeds 80%.

### Features

* Monitoring
* Logging
* Alarms
* Dashboards

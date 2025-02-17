### Module 2

- AWS organization consolidates multiple accounts into one organization.
- IAM is different than applying policies to OUs. IAM applies policies to IAM users, groups, and roles within an AWS account.
- In AWS organizations Service control policies (SCPs) enable you to allow or deny access to services for individual accounts or an OU containing multiple accounts.
- These SCPs affect all the IAM users, groups, and roles in the account, this also includes the root user. 
----
### Module 3

- Route 53 is a DNS service
- AWS cloudfront is a service that is used to distribute content to users to reduce latency
- Requests to those services will be routed to the nearest edge location.
- Edge locations are data centers that serve the content to users. (CloudFront uses to cache copies of your content for faster delivery to users at any location).
- ==`Edge locations`== are mainly used for cloudfront (focused on content delivery)
- ==`Points of presence`== are used by cloudfront, route53, aws shield and waf.
- Edge Locations are a subset of Points of Presence.
- Regional edge caches are used with cloudfront by default, it is used with content that is not accessed frequently enough to be stored in an edge location.
---
### Module 4

- IAM is used to control access to operations on AWS resources using polices (launch, configure, terminate).
- IAM handles authentication and authorization of users.
- IAM scope is global, across all regions.
- IAM roles can be assumed by a user, an app, or a service, this will provide temporary credentials to access the required resource (like sudo in linux).
- Roles are used to delegate access to IAM entities that don't normally have access to the resources.
- All permissions are implicitly denied by default, any action should be explicitly allowed first if needed.
- Explicit denies always precedes an explicit allow.
- Identity based polices are attached to an IAM entity (user, group, role)
- Resource based polices are attached to some AWS resources.
- A user can belong to multiple groups.

- Secure AWS account:
	- Stop using root and create IAM admin.
	- Disable root access keys.
	- Use MFA & cloudtrail.
	- Enable billing reports.

- AWS organizations cannot give access to resources, but can rather block access only.
- SCPs never grants permissions to IAM entities in the organization, but specify the maximum available permissions for an organization (sets the limit of what permissions can be given to IAM entities).
- The effective permissions are the logical intersection between what is allowed by the SCP and what is allowed by the identity and resource-based policies.

----
### Module 5

- Subnets belong to a single availability zone.
- CIDR blocks of subnets cannot overlap.
- AWS reserves 5 addresses from each subnet:
	- .0 -> network address
	- .1 -> internal communication
	- .2 -> DNS resolution
	- .3 -> future use
	- .255 -> network broadcast
- Every EC2 instance is provided a private address upon creation to the default network interface, and the instance can be assigned a public one by the auto-assign public IP settings at the subnet level.
- Elastic IP address is a static IPv4 address associated with an AWS account
	- It is not a free service.
	- Better be allocated to a virtual interface than directly to an instance, as it will be easier to move the interface in event of instance failure. The traffic will be redirected to the new attached instance.

![[Pasted image 20240811174512.png]]


- VPC sharing allows AWS accounts in one organization to share a subnets in a VPC.
- VPC peering allows traffic flow between 2 VPCs, you can only have one peering resource between 2 VPCs.
- We can connect our VPC to on-prem data center by creating a virtual gateway and adding a route pointing to our infra in the route table. Then creating a site-to-site VPN connection.
- VPC endpoints are used to connect your VPC resources to the ==`regional services like S3 and dynamoDB`==. There are to types of
- Gateway endpoints ![[6dac56b4-b5e3-4ee8-854d-31952581440f.avif]]
- Interface endpoints ![[f7712e05-1dbf-46cd-8c87-75a772efee18.avif]]


![[Pasted image 20240811191325.png]]

---
### Module 6

- Instance naming: t3.large
	- T -> family name
	- 3 -> generation number
	- large -> size (refers to vCPU and Mem.)
- EC2 instances are placed on different resources at the AWS data center, we can influence this decision by placing instances in cluster placement group. This can improve improve network latency between the instances.
- EBS volumes are persistent and the instances with an EBS volume attached as the root volume can be stopped and started again without losing the data.
- EC2 instance store provides storage on disks that are attached to the host computer running the EC2 instance, these instances cannot be started again after being stopped as the data stored is deleted.
- EBS and EC2 instance store only can be used for the root volume.
- ![[Pasted image 20240822140152.png]]
- Rebooted instances stay on the same physical host, have the same public DNS name and public IP if assigned one and retains data on instance store volumes.
- Starting a stopped instance will move it to a new physical host and will have a new public IP and an external DNS hostname. (private IP and internal DNS hostname do not change)
- Can use Elastic IP address to maintain a persistent public IP.

- A DNS hostname is a name that uniquely and absolutely names a computer; it's composed of a host name and a domain name. DNS servers resolve DNS hostnames to their corresponding IP addresses.

- EC2 models:
	- Dedicated instances guarantees that your EC2 instance will be running on hardware that's not shared with another AWS customer. Dedicated hosts also does this, but lets you specify the host, once it's been set aside for your exclusive use.
	- Using dedicated hosts is suitable for BYOL.
---
### Module 7

- EBS volumes are replicated in its availability zone for redundancy.
- EBS volumes' scope is the availability zone.
- EBS snapshots (backups) are automatically sent to S3 buckets.
- Snapshots are regional, and cover only what is different from the previous snapshots.
- Only SSDs are used for boot volumes for EC2 instances
- Amazon EBS pricing includes three factors:
	- **Volumes**: Volume storage for all Amazon EBS volume types is charged by the amount of GB you provision per month, until you release the storage.
	- **Snapshots**:  Amazon EBS does not save empty blocks. Copying Amazon EBS snapshots is charged based on the volume of data transferred across Regions. After the first baseline snapshot is copied, standard Amazon EBS snapshot charges apply for storage in the destination Region.
	- **Data transfer:** Inbound data transfer is free and outbound data transfer charges are tiered. If you use external or cross-region data transfers, additional EC2 data transfer charges will apply.

- S3 buckets are named uniquely in the whole world.
- Access to s3 can be managed with IAM policies, S3 bucket policies, or per object ACL (ACLs are legacy and not used anymore, only used for per object)
- Can setup event notifications for upload, delete, etc.
- In S3, data is stored in at least 3 AZs and can be replicated in regions with the cross region replication option
- S3 has multiple storage classes each with their suitable use case
	- You can configure S3 storage classes at the object level
	- S3 glacier data retrieval -> few minutes to hours (tiers)
	- S3 deep glacier archive -> 12 hours (used for regulatory requirements)
- S3 has 2 URL endpoint styles:
	- Bucket path-style -> used for accessing objects
	- Bucket virtual-hosted-style -> used for static websites
- Pay for what you use:
	- GB per month
	- Transfer out to other regions
	- PUT, COPY, POST, LIST and GET requests
	- Transfers between S3 buckets or from Amazon S3 to any service(s) within the same AWS Region are free.

- EFS uses NFS 4.0 or 4.1
- EFS can be mounted on one subnet in each AZ
-  ![[Pasted image 20240904110605.png]]
- ![[Pasted image 20240904105919.png]]

- Terms:
	- Archive: object that is stored in S3 glacier, base unit of storage which has an ID and description
	- Vault: a container for storing archives
		- Each vault has a name and region
		- Can use vault access policies to define access control
		- Vault lock is used to protect integrity of vault
		- Each vault has a vault access policy and a lock
- S3 lifecycle policies move or delete objects based on age.
- Data retrieval fees in glacier costs more
- Data is encrypted by default in glacier and there are various key management that can be used

---
### Module 8

- The basic building block of Amazon RDS is DB instance.
- DB instance is an isolated DB environment that contains multiple created databases.
- When creating RDS instance we choose:
	- DB instance class -> compute part
	- DB instance storage -> storage part
	- DB engine
- In HA deployment, AWS creates another instance which is put in standby mode and synchronizes it with the primary instance.
- Read replicas are used for read-heavy workloads, they can also be promoted to master instances.
- Billing:
	- Clock-hour billing: paying for when the service is running.
	- DB physical characteristics
	- On demand instances or reserved (upfront payment with a contract)
	- Backup storage???
	- Inbound transfers are free while outbound transfers are tiered.
![[Pasted image 20240919100702.png]]

- DynamoDB terms:
	- Items: group of attributes that are uniquely identifiable
	- Tables: collection of data
- All data is stored on SSDs
- Global tables is a function used to replicate tables across regions
- Tables, items, attributes are core DynamoDB components
- Supports single key (partition key) and compound keys (partition key & sort key)

- Redshift is a fully managed data warehouse
- Enables us to run analytics queries against petabytes of structured data
- ![[Pasted image 20240921200932.png]]
---
### Module 9

- Architect is a middleman between the customer (Decision maker) and the building crew (Delivery team)
- He insures the alignment of technological deliverables and business goals
- Aws Well-Architected Framework:
	- [5 pillars](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html):
		- Operational excellence
		- Security
		- Reliability
		- Performance efficiency
		- Cost optimization
	- Each pillar includes design principles and best practices
	- In each best practice area there are a set of questions and question contexts


- Reliability is a measure of the system's ability to provide functionality when desired by a user
- It is the probability that the system will function over a period of specified time
- MTBF is mean time between failures = total time in service/number of failures
- ![[Pasted image 20241001160146.png]]
- Availability is **a percentage uptime (such as 99.9%) over a period of time (commonly a month or year)**
- Availability = uptime / total time
- Five nines = 99.999
- Fault tolerance is the built in redundancy of an app's components, it only addresses HW failures. [check](https://www.ibm.com/docs/en/powerha-aix/7.2?topic=aix-high-availability-versus-fault-tolerance)
- Fault tolerance > high availability
---
### Module 10

- Load balancers are regional, they can distribute to a single or multiple AZs
- 3 Types:
	- Application load balancer: 
		- Layer 7, HTTP & HTTPS
		- Routes based on content of requests
	- Network load balancer
		- Layer 4, TCP UDP TLS
		- Route based on IP protocol data
	- Classic load balancer (older gen)
- ![[Pasted image 20241002173516.png]]

-  EC2 auto scaling provides several options: Manual, scheduled, dynamic, on-demand, predictive.
- ![[Pasted image 20241006153122.png]]
- ![[Pasted image 20241006153341.png]]
- Predictive scaling uses actual historical data of your workloads, and creates a forecast for the expected usage
- Scaling policies are policies you define (i.e CPUutiliztation in EC2)
- ![[Pasted image 20241006154159.png]]
-
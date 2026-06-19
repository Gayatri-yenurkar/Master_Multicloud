Cloud Infrastructure Interview Guide: AWS, Azure, and GCP (Provider-Specific)

This file contains targeted interview questions and scenario-based technical deep-dives for individual cloud providers. Each section focuses exclusively on that provider's native terminology, architecture, and deployment constraints regarding Regions, Zones, and Hardware Resiliency.

Section 1: Amazon Web Services (AWS)
Q1: What is the architectural difference between an AWS Availability Zone (AZ) and an AWS Local Zone?
Interviewer Intent: Testing knowledge of edge computing and specialized AWS infrastructure tiers.

Ideal Answer: * An Availability Zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region. They are designed for standard, highly available multi-tier enterprise workloads.

An AWS Local Zone places compute, storage, database, and other select AWS services closer to large population, industry, and IT centers. It is an extension of an existing AWS Region but physically located closer to the end-users to support ultra-low latency applications (under 10 milliseconds), such as media streaming, real-time gaming, or electronic design automation.

Q2: You are deploying an auto-scaling application across three AZs in us-east-1. How can you programmatically guarantee that your mission-critical EC2 instances do not share the same underlying physical server rack or power source within those AZs?
Interviewer Intent: Verifying if the candidate knows how to control rack-level placement in AWS.

Ideal Answer:

To guarantee physical rack-level isolation within an AZ, you must use Spread Placement Groups.

When you launch instances inside a Spread Placement Group, AWS ensures that each instance is placed on a distinct hardware rack, each with its own network and power source.

You can span a single Spread Placement Group across multiple AZs within the same region, allowing you to have up to 7 running instances per AZ per group.

Q3: A company has an application running on EC2 instances inside an Auto Scaling Group (ASG) spanned across 3 AZs in the eu-west-1 region. If the entire eu-west-1 region suffers a total catastrophic infrastructure failure, will the ASG automatically spin up instances in eu-central-1? Explain why or why not.
Interviewer Intent: Confirming the candidate understands the hard boundaries of AWS regional control planes.

Ideal Answer:

No, it will not. An AWS Auto Scaling Group is a regional resource bounded strictly to the region in which it was created. It can scale instances across different AZs within that specific region, but it has no visibility or authority over another AWS region.

To survive a total regional failure, you must architect a Cross-Region Disaster Recovery strategy. This requires setting up a separate ASG in eu-central-1, configuring cross-region database replication (such as Aurora Global Databases), and using Route 53 Application Recovery Controller or latency/failover routing policies to redirect DNS traffic to the secondary region.

Section 2: Microsoft Azure
Q1: Explain the difference between an Azure Availability Zone and an Azure Availability Set. When would you use each?
Interviewer Intent: Testing the candidate's understanding of Azure’s legacy vs. modern high-availability primitives.

Ideal Answer:

Azure Availability Zones are physically separate data center locations within an Azure region, each with independent power, cooling, and networking. You use them to protect applications from datacenter-level failures (e.g., a localized flood or substation power failure).

Azure Availability Sets are logical groupings within a single data center facility. They isolate Virtual Machines at the hardware rack level by distributing them across different Fault Domains and Update Domains. You use Availability Sets for older Azure regions that do not support native Availability Zones, or for legacy workloads that cannot handle the slight latency overhead of cross-AZ communication.

Q2: You have deployed 5 Virtual Machines inside an Azure Availability Set. Explain exactly how Azure protects these VMs during a routine hypervisor host patching event versus a sudden hardware failure.
Interviewer Intent: Evaluating deep understanding of Azure Fault Domains (FDs) and Update Domains (UDs).

Ideal Answer:

During a sudden hardware failure (Fault Domain): Azure automatically distributes the 5 VMs across physical hardware racks (usually up to 3 FDs). If a specific server rack loses power or its network switch fails, only the VMs on that specific FD go down. The remaining VMs on the other FDs continue running.

During a routine patching event (Update Domain): Azure assigns VMs to logical Update Domains (up to 20 UDs). When Microsoft updates the underlying host hypervisors, it reboots hosts one UD at a time. Azure ensures that only one UD is taken offline for patching at any given moment, ensuring the application remains accessible via the VMs sitting in the other UDs.

Q3: You are designing an ultra-low latency high-performance computing (HPC) cluster on Azure VMs. Even within the same data center zone, physical distance between server rows causes microsecond delays. How do you solve this in Azure?
Interviewer Intent: Testing knowledge of proximity-based hardware alignment in Azure.

Ideal Answer:

To achieve the absolute lowest possible network latency, you must deploy the VMs inside an Azure Proximity Placement Group (PPG).

A PPG is a logical grouping capability that forces Azure to physically locate your virtual machines as close to each other as possible within the same physical data center building, down to the same server rack or adjacent racks. This minimizes physical fiber distance and reduces network round-trip time (RTT).

Section 3: Google Cloud Platform (GCP)
Q1: In GCP Compute Engine, what is the functional difference between a Regional Managed Instance Group (MIG) and a Zonal MIG?
Interviewer Intent: Assessing understanding of Google's multi-zone automation and orchestration layers.

Ideal Answer:

A Zonal MIG deploys and manages a collection of identical VM instances within a single, specific GCP Zone (e.g., us-central1-a). If that specific zone experiences an outage, all instances in the MIG could become unavailable.

A Regional MIG automatically distributes VM instances across multiple zones within the same parent region (e.g., spreading instances across us-central1-a, us-central1-b, and us-central1-c). It provides higher availability because if a single zone fails, the MIG can automatically scale up or redistribute instances in the surviving zones to maintain application throughput.

Q2: You are hosting a highly transactional distributed database cluster on Google Compute Engine. You require strict, absolute physical rack isolation for your instances to ensure a hardware failure never takes down quorum. How do you configure this?
Interviewer Intent: Testing knowledge of GCP's explicit hardware placement abstractions.

Ideal Answer:

You must explicitly create and apply a Spread Placement Policy to the Compute Engine instances.

By applying a Spread Placement Policy, you programmatically instruct GCP's internal scheduler (Borg) to place each VM on an entirely separate physical hardware rack (separate fault domain) within the chosen zone. This ensures that a localized top-of-rack switch failure or power distribution unit (PDU) failure only impacts a single database node. Note that GCP enforces a strict limit of up to 8 VMs per zone per spread policy to guarantee this isolation.

Q3: How do GCP Dual-Regions differ from standard Multi-Regions when configuring Google Cloud Storage (GCS) buckets for disaster recovery?
Interviewer Intent: Testing architectural knowledge of GCP's storage replication boundaries.

Ideal Answer:

A GCP Multi-Region geo-replicates data across a wide, generalized geographic area (like the entire United States or the European Union) spanning at least two geographic places separated by at least 100 miles. It offers high durability but less control over exactly where data resides.

A GCP Dual-Region allows you to explicitly choose exactly two specific regions within a continent (e.g., Iowa and South Carolina). This provides turbo-charged replication features (such as Turbo Replication, which guarantees data is replicated between the two distinct regional boundaries within 15 minutes), meeting strict compliance and data residency laws while still offering full geo-redundancy if one region goes completely offline.

Section 4: Oracle Cloud Infrastructure (OCI)
Q1: What is the structural difference between an OCI Availability Domain (AD) and an OCI Fault Domain (FD), and how do they dictate your high-availability strategy?
Interviewer Intent: Verifying the candidate understands OCI's core physical infrastructure boundaries.

Ideal Answer:  An Availability Domain (AD) is one or more data centers located within a region. ADs are isolated from each other, do not share infrastructure such as power or cooling, and are highly unlikely to fail simultaneously. You use multi-AD deployments to protect against total data center disasters.

A Fault Domain (FD) is a logical grouping of hardware and infrastructure within an Availability Domain. Each AD contains exactly three Fault Domains (FD1, FD2, FD3). They act as anti-affinity groups at the server-rack level. You use FDs to ensure your instances do not share the same physical hardware, top-of-rack switches, or power distribution units, protecting your app from localized hardware failures.

Q2: OCI has introduced many "Single-AD" regions globally. If you are deploying a critical application in a Single-AD region, how do you architect it for high availability without failing over to another geographic region?
Interviewer Intent: Testing the candidate's hands-on experience with OCI's specific regional architecture and design constraints.

Ideal Answer:

In a Single-AD region, you cannot replicate workloads across multiple Availability Domains. Therefore, your high-availability strategy must rely entirely on Fault Domains.

You must distribute your application components (e.g., web servers, application servers, database nodes) symmetrically across FD1, FD2, and FD3.

OCI guarantees that a hardware failure or maintenance event in one Fault Domain will not impact the other two. By architecture-level distribution across all three FDs, you eliminate single points of physical rack failure, ensuring the application remains up even if a top-of-rack switch or power supply line blows.

Q3: When you deploy an Oracle Base Database Service or an instance requiring Block Volumes across Fault Domains, how does OCI handle storage attachment and data durability at the FD layer?
Interviewer Intent: Understanding if the candidate knows how OCI’s block storage mesh interacts with compute placement boundaries.

Ideal Answer:

OCI Block Volumes are stored independently of the compute racks and are replicated automatically across multiple storage servers within the Availability Domain to guarantee durability.

When a compute instance in FD1 attaches to a Block Volume, the storage itself is accessible across the entire AD network. If the physical compute rack in FD1 fails, the block volume is not lost.

To architect for database or state durability, you deploy a secondary instance in FD2. You can then use application-level replication (like Oracle Data Guard) or unattach/re-attach the block volume to the surviving instance in FD2 to recover operations, because the storage layer cuts across the individual compute Fault Domain boundaries.
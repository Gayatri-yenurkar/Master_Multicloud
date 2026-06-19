# **Multi-Cloud Infrastructure Interview Guide: Regions, Zones, and Fault Domains**

This guide contains production-level interview questions and scenario-based technical deep-dives focused on multi-cloud infrastructure design across AWS, Azure, GCP, and OCI. It covers geographic isolation, data center boundaries, and hardware rack-level resiliency.

## **Part 1: Structural Mapping & Terminology (Mid-Level)**
Q1: Translate how a hardware-level "Fault Domain" in Oracle Cloud Infrastructure (OCI) or Azure maps to AWS and GCP environments.
Interviewer Intent: Testing whether the candidate understands the lowest physical layer of cloud infrastructure (server rack level) across different vendors.

Ideal Answer:

In OCI and Azure, a Fault Domain (FD) is explicitly exposed to the user. It represents a logical grouping of hardware/server racks within a single data center that shares a common power source and top-of-rack network switch.

AWS does not explicitly expose "Fault Domains" by default. Instead, to achieve the same rack-level isolation within an Availability Zone, you must deploy instances into a Spread Placement Group.

GCP abstracts this layer completely via its cluster management software (Borg), which automatically distributes instances across different physical racks within a Zone. For strict programmatic enforcement, you must explicitly apply a Spread Placement Policy.

Q2: Why is the distinction between an Availability Zone (AZ) and a Fault Domain (FD) critical when designing a multi-cloud business continuity plan?
Interviewer Intent: Evaluating if the candidate understands the failure blast radius of physical infrastructure.

Ideal Answer: They address completely different failure boundaries:

Availability Zones / Availability Domains / GCP Zones protect against facility-level disasters (e.g., utility power grid failure, local flooding, fires). The blast radius is the entire data center building or cluster of buildings.

Fault Domains / Placement Groups protect against localized hardware-level failures (e.g., a bad power distribution unit or a blown top-of-rack network switch). The blast radius is limited to a single server rack.

Architectural Note: If an entire AZ goes offline due to a massive power grid failure, your Fault Domains inside that AZ are useless. True high availability requires cross-AZ/cross-Zone distribution.

## **Part 2: Scenario-Based Architecture Problems (Senior Level)**
Q3: "We are hosting an application frontend in AWS us-east-1 (N. Virginia) and our transactional database in OCI us-ashburn-1 (N. Virginia). During a traffic spike, application latency spikes dramatically, but CPU and memory utilization across both clouds are under 40%. What infrastructure design flaw should you investigate?"
Interviewer Intent: Testing the candidate's understanding of the #1 multi-cloud pitfall: cross-cloud network routing and geographic region matching.

Ideal Answer:

Investigate Cross-Cloud Interconnect Routing: Even though both regions are located in Northern Virginia, traffic between AWS and OCI does not automatically route locally unless explicitly configured. If traffic is routing over the public internet, it may be taking an inefficient path through multiple external Internet Service Providers (ISPs), introducing massive latency.

Verify Availability Zone to Availability Domain Mapping: Cloud providers randomize the naming of their internal AZs/ADs for capacity balancing (e.g., AWS us-east-1a for Account A might be physical data center X, but for Account B it is physical data center Y). You need to benchmark the specific latency between the specific AWS AZ subnet and the OCI AD to ensure they aren't physically sitting on opposite sides of the state.

Remediation: Implement a dedicated, low-latency private circuit (such as the AWS-to-OCI FastConnect Interconnect or a third-party fabric provider like Megaport or Equinix) to bypass the public internet and pin cross-cloud latency to under 2ms.

Q4: "You are deploying a highly available distributed database cluster in an OCI region that contains only one single Availability Domain (AD). How do you architect this to guarantee high availability without failing over to a different geographic region?"
Interviewer Intent: Testing specialized knowledge of OCI’s single-AD region architecture and how to utilize rack-level segregation.

Ideal Answer:

Because many newer OCI regions are optimized as single-AD regions, you must leverage Fault Domains as your primary layer of physical isolation.

You should deploy your database nodes in multiples of three and distribute them explicitly across Fault Domain 1 (FD1), Fault Domain 2 (FD2), and Fault Domain 3 (FD3).

Configure your application logic or database load balancer to treat these FDs as virtual zones. Because OCI guarantees that no two FDs within an AD share the same power supply or top-of-rack network switches, a physical hardware failure on a rack will only take down one node, leaving the other two FDs operational to maintain cluster quorum.

## **Part 3: Advanced Core Boundaries & "Gotchas" (Lead / Principal Level)**
Q5: "Can you design a single Virtual Private Cloud (VPC) subnet or virtual network that spans across an AWS Availability Zone and a GCP Zone simultaneously to simplify cluster networking?"
Interviewer Intent: Verifying if the candidate knows the absolute technical boundaries of cloud-native networking layers.

Ideal Answer:

Absolutely not. Layer 2 and native Layer 3 software-defined networking structures (VPCs in AWS, VNets in Azure, VPCs in GCP) are entirely proprietary to their respective hyperscaler backbones.

A subnet is bounded by the cloud provider's virtual router infrastructure. To connect an AWS AZ to a GCP Zone, you must build separate virtual networks in each cloud, establish an explicit Layer 3 routing link between them (via a Site-to-Site IPSec VPN or a private dedicated circuit), and handle the cross-cloud traffic via standard IP routing.

Q6: "If a cloud provider suffers a complete regional outage (e.g., US-East completely drops offline), how do Fault Domains or Placement Groups factor into your disaster recovery strategy?"
Interviewer Intent: A trick question designed to catch candidates who mix up high availability (HA) with disaster recovery (DR).

Ideal Answer:

They do not factor in at all. Fault Domains and Placement Groups provide intra-datacenter / intra-zone resiliency. They protect against localized hardware issues.

If a complete regional outage occurs, the entire region's power, cooling, or core regional control plane API has collapsed. Because Fault Domains are nested inside the region, they fail with the region.

To survive a regional outage, the architecture must implement a true Cross-Region Disaster Recovery strategy (Active-Active or Active-Passive) using distinct geographic regions entirely (e.g., replicating state from AWS us-east-1 to us-west-2 or cross-cloud to OCI us-phoenix-1).


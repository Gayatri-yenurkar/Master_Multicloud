# **☁️ Oracle Cloud Infrastructure (OCI)**

## **1. OCI Region (The Geographic Layer)**

"An OCI Region is a localized geographic area containing Oracle's data centers. We strategically choose our primary and secondary regions to minimize latency for our users and to provide a failover site in case of a major geographic disaster."

## **2. Availability Domain / AD (The Facility Layer)**

"An Availability Domain is a highly secure, isolated data center facility within an OCI region. ADs do not share power or cooling with one another. If one AD is compromised by a physical disaster, the others remain fully operational."

## **3. Fault Domain / FD (The Hardware Layer)**

"A Fault Domain is a logical grouping of physical hardware within an Availability Domain. OCI automatically provides three Fault Domains per AD. By distributing our instances across FD1, FD2, and FD3, we guarantee they don't share the same server rack or power source, protecting us from localized hardware failures."
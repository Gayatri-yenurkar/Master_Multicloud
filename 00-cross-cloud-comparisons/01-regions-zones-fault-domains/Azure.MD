# **☁️ Microsoft Azure**

## **1. Azure Region (The Geographic Layer)**

"An Azure Region is a designated perimeter on the globe containing one or more data centers. We use distinct regions to comply with data residency laws and to survive massive, city-wide disasters."

## **2. Availability Zone / AZ (The Facility Layer)**

"An Azure Availability Zone is a unique, physically isolated facility within an Azure region, equipped with independent power, cooling, and networking. Deploying across multiple AZs guarantees that a fire or utility failure at one data center won't take down our application."

## **3. Fault Domain / FD (The Hardware Layer)**

In Azure, the underlying physical failure boundary—essentially a single server rack sharing power and network switches—is called a Fault Domain. However, as an architect, you don't pick the rack manually. You deploy your Virtual Machines into an Availability Set (or a Virtual Machine Scale Set), and Azure automatically distributes them across multiple Fault Domains to protect against localized hardware failures."  
**☁️ Microsoft Azure**

1. Azure Region (The Geographic Layer)

"An Azure Region is a designated perimeter on the globe containing one or more data centers. We use distinct regions to comply with data residency laws and to survive massive, city-wide disasters."

2. Availability Zone / AZ (The Facility Layer)

"An Azure Availability Zone is a unique, physically isolated facility within an Azure region, equipped with independent power, cooling, and networking. Deploying across multiple AZs guarantees that a fire or utility failure at one data center won't take down our application."

3. Fault Domain / FD (The Hardware Layer)

"A Fault Domain represents a single physical server rack inside the data center. By ensuring our virtual machines are distributed across different Fault Domains—usually via an Availability Set—we protect the application from a localized rack failure, like a blown power supply."
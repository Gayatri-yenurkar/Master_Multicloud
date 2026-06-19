**☁️ Amazon Web Services (AWS)**

1. AWS Region (The Geographic Layer)

"An AWS Region is a specific geographic location—like Northern Virginia or Tokyo—where AWS operates multiple, isolated data center clusters. If an entire region goes down due to a massive natural disaster, we rely on cross-region replication to keep the business running."

2. Availability Zone / AZ (The Facility Layer)

"An Availability Zone is a physically separate data center facility within an AWS Region. It has its own independent power, cooling, and network. We distribute our application across multiple AZs to ensure that if one building floods or loses power, the application stays online."

3. Spread Placement Group (The Hardware Layer)

"A Spread Placement Group limits the blast radius of a localized hardware failure. It guarantees that our EC2 instances are placed on completely different physical server racks inside the data center. If a top-of-rack network switch or power strip dies, it only affects one instance, not the whole cluster."
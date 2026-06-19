**☁️ Google Cloud Platform (GCP)**

1. GCP Region (The Geographic Layer)

"A GCP Region is a specific geographical location where Google hosts its infrastructure. Because regions are geographically distant, we use them to build disaster recovery plans to survive massive events like hurricanes or regional grid failures."

2. GCP Zone (The Facility Layer)

"A Zone is a deployment area within a GCP Region. Think of it as an independent data center facility with its own power and cooling. We deploy workloads across multiple zones so that if one building experiences a catastrophic failure, traffic automatically routes to the surviving zones."

3. Spread Placement Policy (The Hardware Layer)

"A Spread Placement Policy enforces hardware-level separation. It instructs Google's scheduler to place our virtual machines on completely distinct physical hardware racks. This ensures a localized hardware failure—like a bad top-of-rack switch—only impacts a single node, not the whole system."
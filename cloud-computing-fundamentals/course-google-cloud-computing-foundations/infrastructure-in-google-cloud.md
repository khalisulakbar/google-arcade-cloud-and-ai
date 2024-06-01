# Google Cloud Computing Foundations: Infrastructure in Google Cloud

## Overview
- Where do I store this stuff?
- There is an API for that
- You can't secure the cloud, right?


## 1. Where do I store this stuff?
> Every application needs to store data, like media to be streamed or perhaps even sensor data from devices, and different applications and workloads require different storage database solutions. Choosing the right option to store and process data often depends on the data type that needs to be stored and the business need.

In google cloud, there are several storage offerings, which are?
1. Relational Databases
2. Non-relational Databases
3. Worldwide Object Storage

Google Cloud provides managed storage and database services that are scalable, reliable, and easy to operate. These include: 
- Cloud Storage, 
- Cloud SQL, ------> Relational Databases
- Cloud Spanner,---> Relational Databases
- Firestore, and --> NoSQL Databases
- Cloud Bigtable --> NoSQL Databases

There are three common cloud storage use cases.
1. The first is content storage and delivery -> This is when content, such as images or videos, needs to be served to users wherever they are.
2. The second use case is storage for data analytics and general compute -> Users can process or expose their data to analytics tools.
3. The third use case is backup and archival storage -> Users can save storage costs by migrating infrequently accessed content to cheaper cloud storage options.
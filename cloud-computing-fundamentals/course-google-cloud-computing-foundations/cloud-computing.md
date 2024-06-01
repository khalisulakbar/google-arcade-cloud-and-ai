# CLOUD COMPUTING

## 1. Overview

**What is cloud computing?**
> Cloud computing is a form of computing that is accessed through the internet. Cloud computing is a way of using information technology (IT) that has these five equally important traits.

1. First, customers get computing resources that are on-demand and self-service.
2. Second, customers get access to those resources over the internet, from anywhere they have a connection.
3. Third, the provider of those resources has a large pool of them, and allocates them to users out of that pool.
4. Fourth, the resources are elastic–which means they can increase or decrease as needed, so customers can be flexible.
5. And finally, the customers pay only for what they use, or reserve as they go.

**Cloud computing vs. traditional architecture**
> Ultimately, Cloud computing offers cost-savings, scalability, and the ability to access data from anywhere. Traditional computing requires businesses to purchase and manage hardware, software, and other related services, which can be expensive and difficult to scale. [source](https://www.simplilearn.com/cloud-computing-vs-traditional-computing-article#:~:text=Ultimately%2C%20Cloud%20computing%20offers%20cost,expensive%20and%20difficult%20to%20scale.)

<img src="cloud vs on-premise.png" alt="alt text" style="width:600px;height:auto;">

[image source](https://www.techrepublic.com/article/cloud-computing-vs-traditional-architecture/)

**Google Cloud Architecture**

``` 
    +---------------------------------------+
    |         BIG DATA & ML PRODUCTS        |
    +-------------------+-------------------+           
+---------------------+  +-----------------------+
|      Compute        |  |       Storage         |
+---------------------+  +-----------------------+             
    +---------------------------------------+
    |        NETWORKING & SECURITY          |
    +---------------------------------------+
```

Google offers a range of computing services, which includes:
1. Compute Engine
2. Google Kubernetes Engine
3. App Engine
4. Cloud Functions
5. Cloud Run

Google Cloud also offers a variety of managed storage options. The list includes:
1. Cloud Storage
2. Cloud SQL    (Relational Database)
3. Cloud Spanner (Relational Database)
4. Cloud Bigtable (NoSQL Database)
5. Firestore (NoSQL Database)

And then there’s a robust big data and machine learning product line.
This includes: 
1. Cloud Storage
2. Dataproc 
3. Bigtable
4. BigQuery
5. Dataflow
6. Firestore
7. Pub/Sub
8. Looker
9. Cloud Spanner
10. AutoML, and
11. Vertex AI


## 2. Google Cloud Platform
> There are four ways to interact with Google Cloud: Google Cloud Console, the Cloud SDK and Cloud Shell, the APIs, and the Cloud Mobile App.

### 1. Google Cloud Console



###### **google cloud billing, Billing is established at the project level. It means that, when we creating a project, we have to create a billing account. a billing account can be used for several projects. If a project is not linked to a billing account, it can be only used for free Google Cloud services.

### 2. The Cloud SDK


### 3. Cloud Shell

 - `gcloud auth list` -> List all accounts you have access to
 - `gcloud config list project` -> List all projects you have access to
 - `gcloud config get-value compute/region` -> to check the region the project activates
 - `gcloud config set compute/region europe-west1` -> set the region e.g: europe-west1
 - `gcloud config get-value compute/zone` -> check the zone the project activates
 - `gcloud config set compute/zone europe-west1-b` -> set the zone e.g: europe-west1-b
 - `gcloud compute project-info describe --project $(gcloud config get-value project)` -> to check the project details


1. Setting Environment Variables:
- `export PROJECT_ID=$(gcloud config get-value project)` -> Create an environment variable to store your Project ID, e.g: qwiklabs-gcp-04-5366dee3e897
- `export ZONE=$(gcloud config get-value compute/zone)` -> Create an environment variable to store your Zone, e.g: europe-west1-d
- `echo -e "PROJECT ID: $PROJECT_ID\nZONE: $ZONE"` -> To verify that your variables were set properly

2. Creating a virtual machine with Gcloud Tool:
To create a virtual machine, run the following command:
```
gcloud compute instances create gcelab2 --machine-type e2-medium --zone $ZONE' 
```

Command details

- gcloud compute allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API.
- instances create creates a new instance.
- gcelab2 is the name of the VM.
- The --machine-type flag specifies the machine type as e2-medium.
- The --zone flag specifies where the VM is created.
- If you omit the --zone flag, the gcloud tool can infer your desired zone based on your default properties. Other required instance settings, such as machine type and image, are set to default values if not specified in the create command.

`gcloud compute instances create --help` -> to get more details

3. Exploring gcloud commands:

- `gcloud compute instances list` -> List all instances
- `gcloud compute instances list --filter="name=('gcelab2')"` -> List all instances with the name gcelab2
- `gcloud compute instances describe gcelab2` -> to get more details
- `gcloud compute firewall-rules list` -> List all firewall rules
- `gcloud compute firewall-rules list --filter="network='default'"` -> List all firewall rules with the network default
- `gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"` -> List all firewall rules with the network default and allow icmp

4. Connecting to your VM instance:

To connect to your VM instance, run the following command:
```
gcloud compute ssh gcelab2 --zone $ZONE
```

5. Updating the firewall:

To update the firewall, run the following command:
```
gcloud compute firewall-rules list
```

WHen we try to access the nginx service running on the gcelab2 virtual machine,  Communication with the virtual machine will fail as it does not have an appropriate firewall rule. The nginx web server is expecting to communicate on tcp:80. To get communication working you need to:

- Add a tag to the gcelab2 virtual machine
```
gcloud compute instances add-tags gcelab2 --tags http-server,https-server
```

- Add a firewall rule for http traffic
```
gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```

Now, if we `gcloud compute firewall-rules list --filter=ALLOW:'80'`, we will see the new firewall rule.

Lastly, communication is possible for http to the virtual machine:
```
curl http://$(gcloud compute instances list --filter=name:gcelab2 --format='value(EXTERNAL_IP)')
```

we will see the default `nginx` output.
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

6. Viewing the system logs:

To view the available logs on the system:, run the following command:
```
gcloud logging logs list
```

To view the logs that relate to compute resources:
```
gcloud logging logs list --filter="compute"
```

To Read the logs related to the resource type of `gce_instance`:
```
gcloud logging read "resource.type=gce_instance" --limit 5
```

To Read the logs for a specific virtual machine:
```
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```

### 4. The Cloud Mobile App


## 3. Using Google Cloud To Build Apps

Google offers a range of computing services, which includes:
1. Compute Engine (Iaas)
2. App Engine (PaaS)
3. Cloud Functions (Serverless logic)
4. Google Kubernetes Engine (Hybrid)
5. Cloud Run (Serverless platform)



### 1. Compute Engine
> Compute Engine allows developers to create virtual machines (VMs) that run different operating systems, including multiple flavors of Linux (Debian, Ubuntu, Suse, Red Hat, CoreOS) and Windows Server, on Google infrastructure. Each virtual machine contains the power and functionality of a full-fledged operating system (It can be configured much like physical servers).

### 2. App Engine
> App Engine allows developers to focus on writing code, and not what it runs on. Developers upload their apps to App Engine, and Google Cloud takes care of the rest. The notion of servers, virtual machines, and instances have been abstracted away, with App Engine providing all the compute necessary. Developers don't have to worry about operating systems, web servers, logging, monitoring, load-balancing, system administration, or scaling, as App Engine takes care of all that. Developers only need to focus on building solutions for their organizations or their users.

### 3. Cloud Functions
> Cloud Function is a piece of a code that runs in response to an event. for example, when an image is uploaded to a bucket, or when image is transformed, etc.

### 4. Google Kubernetes Engine
> Google Kubernetes Engine (GKE) provides a managed environment for deploying, managing, and scaling your containerized applications using Google infrastructure.

### 5. Cloud Run
> Cloud Run allows developers to run code directly in a containerized environment on Google infrastructure (fully managed compute platform).

**NOTES**
####### 1. When creating a VM, `allow http trafic` -> Select this option in order to access a web server that you install later. Note: This automatically creates a firewall rule to allow HTTP traffic on port 80.
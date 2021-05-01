# Google Cloud Platform

GCP includes core infrastructure, databases, analytics, AI and more. The platform has over 100 services spanning IaaS, PaaS, and SaaS.

- Web Console
- Cloud Shell & SDK (administrators / DevOps)
- Mobile App
- REST API

A project represents a billable unit that contains many application and resources.

## Compute Services

Critical cloud component where code is deployed and executed.

### Google Compute Engine (GCE)

Enables Linux and Windows VMs to run on the Google´s global infrastructure, based on machine types with varied CPU and RAM configuration. Infrastructure as a Service (**IaaS**)

For persitance the Vms needs to be attatched to an SSD disk. All VMs are charged a minimum of 1 minute run and 1 second increments after that.

- _Sustained use discounts:_ Offered for VMs that runs for a significant portion of the billing month (regular usage)
- _Committed use discounts:_ Offered by purchases based on 1-3 year contracts.

#### Configure a Cloud VM

_Ubuntu 18.04 LTS demo (apache2) available at http://35.192.140.204_

1. Enter to your GCP Console https://console.cloud.google.com
2. Naviage to `Compute > Compute Engine > VM Instances`
3. Create/import a new Vm instance by assigning the desired OS in the `Boot Disk` tab and by checking the `Allow HTTP/HTTPS traffic` checkbox in the Firewall section.
4. Enter the following commands on the Google Cloud Shell to SSH your VM

```
# Cloud Shell
$ gcloud compute regions list
$ gcloud compute ssh [vm_name] --zone [vm_region]
```

5. By now your VM CLI should be accessible by the `External IP` provided in the VM Instances dashboard.
6. Optionally you can install a HTTP Server such as `apache2` in your VM to test web server connectivity.

### Google Kubernetes Engine (GKE)

Orchestration platform that managed environment for deploying containerized applications. The Kubernetes engine is now part of the called **Cloud Native Computing Foundation**. Container as a Service (**CaaS**).

Delivers **auto-scaling**, **automatic upgrades** and **node auto-repair features**. Is the managed Kubernetes environment where customer provides a cluster and deploy contenarized applications that scale.

Developers can bring their Kubernetes packaged container images and deploy artifacts and scale them through GKE.

Provides a fully scalable distributed computing system (control & data plane) keeping the cluster.

**Node pool**: Segregate cluster environment in different compute capabilities sucha as CPU and high storage throughtput.

### Google App Engine (GAE)

Is a PaaS where developers deploy their code and the system returns an URL where the app is running (Firebase hosting). Platform as a Service (**PaaS**)

Fully managed platform for deploying web apps at scale. When traffic increase the App Engine is capable of scaling without keeping security and access control.

- **Standard Deployment**: Web apps are deployed in a sandbox with certain limitations and constraints.
- **Flexible Deployment**: Uses a Dockerized container image where all the dependencies can be defined in order to have more control in the scallation process.

### Google Cloud Functions

**Serverless** execution environment for building and connecting cloud services. Functions as a Service (**FaaS**)

Developers can deploy API backend functions and consume them as a service (Firebase Cloud Functions). Execute code in a response to an trigger event. There is **NO need to provide a computer/vm/container** to run the application and response to an event.

## Storage Services

Storage services add persitance and durability to applications.

- Object storage
- Block file systems
- File systems

The GCP storage services can be used to store:

- Unstructured data
- Folders and files

### Google Cloud Storage (GCS)

Unified object storage exposed through an API driven access. Can be set for High Frequency Access (high-performance object storage) and Low Frequency Access (backup and archival storage).

_Note: The usage of Persistance Disks (PD) in the usage of Computer Engine VMs provides reliable block storage for GCE VMs (up to 64TB available)_

### Cloud Filestore

_Cloud Filestore demo bucket (Pokemon image collection) available with an authorized API call (OAuth 2.0) to https://storage.googleapis.com/storage/v1/b/htakamif-demo/o_

Delivers NAS-like (fixed export settings and default Unix permissions) filesystem interface and a shared filesystem for managing file storage for applications. Centralized, high-available filesystem for GCE and GKE. Cloud Filestore encrypts data while in transit.

## Network Services

Leverages Google´s global network for connectivity to access the complete GCP applications relying in the standard tiers (ISP backbone) or premium tiers (higher throughtput and low latency same as GCP infrastructure) offered by Google.

### Load Balancers

Distributes traffic across multiple GCE VMs in a single or multiple regions.

- **HTTP/S Load Balancer:** Provides **global** load balancing based on the application endpoint. External load-balancer.
- **Network Load Balancer:** Manages the TCP and UDP routing traffic **within the same region**. Internal load-balancer.

_Note: Both internal and external load-balancers can be used to improve the traffic performance of an application or set of applications in the Google Cloud_

#### Configure a HTTP/S Load-balancer

**Create an Instance Template to define the VMs blueprint**

1. Enter to your GCP Console https://console.cloud.google.com
2. Naviage to `Compute > Compute Engine > Instance templates` and configure the blueprint for the VMs.
3. Create/import a new Vm instance by assigning the desired OS in the `Boot Disk` tab and by checking the `Allow HTTP/HTTPS traffic` checkbox in the Firewall section.
4. Assign the following Debian script (or adapt it to your selected OS) in the `Automation > Startup Script` section so all the VMs start with a running Apache Server and response to it.

```
#! /bin/bash
apt-get update
apt-get install -y apache2
cat <<EOF > /var/www/html/index.html
<html><body><h1>Hello from $(hostname)</h1>
</body></html>
EOF
```

5. Save changes and proceed to create an Instance Group of VMs.

**Using the blueprint, configure the Instance Group to determined how many VMs will be created with it**

1. Naviage to `Compute > Compute Engine > Instance groups` and configure the VMs.
2. Create the instance group profile by entering the desired `Region`, then selection the `Instance Template` previously created, assigning the `Number of instances` or VMs that we want to configure and changing the `Initial delay` to `60`
3. Optionally you can assign a **Health check** that will verify the current state of our VMs and will determined from which one foward traffic (avoiding the faulty servers)

   1. In the `Autohealing > Health Check` section `Create a health check`
   2. Assign it a name, define the `Protocol` and `Port` as `HTTP/HTTPS` and `80/443` (default values)
   3. Define the `Request path` as root route `/`
   4. Assign a `Check interval` with the recommended value of `10` seconds

4. Create the instance group and verify you see the same number of instances (from step 2) in your dashboard

**Configure the HTTP/HTTPs Load Balancer**

_Load-balancer demo (with 2 GCE instances managed by rate) available at http://34.117.96.117:80. Refresh your browser to observe the hostnames changing from one Vm to another_

1. Naviage to `Network > Network services > Load balancing` and create a new load balancer
2. Select `HTTP(s) Load balancer`
3. Choose the type of traffic to be balanced `From internet to my VMs` or `Only between my VMs`
4. Configure the `Backend configuration` as follows:
   1. If you create the **VMs from a template** select `Instance group` checkbox
      1. Select the Instance group name
      2. Select the `Balancing mode` by **Utilization** (manage traffic when resources are decreasing) or by **Rate** (manages traffic per access request from the users)
      3. Assign an `RPM` related to your expected traffic rate
      4. Optionally you can include the `Health check` from the previous steps
   2. If **each VM where uniquely created** select `Network endpoint groups` checkbox
5. Configure the `Frontend configuration` as follows:
   1. Assign a name for the frontend instance
   2. You can leave the setting as default (recommended) or modify the values.
6. Save load balancer configuration and then test using the `IP:Port` provided in the dashboard.

_**Note**: You can see the load balancer in action by accessing the public address of the load balancer and by **refreshing (F5)** the same link. You should see a change in the hostname (if the **$(hostname)**) was provided by the application or in the Apache startup config._

### Virtual Private Cloud

Software defined network providing private networking for VMs. Logically isolates the GCE VMs in a private cluster with regional subnets. Enable hybric-cloud environments using Cloud VPN or Cloud Interconnect.

**VPC Peering:** Utility that interconnect VPC domains, so different VPC subnets can be able to share resources. Firewall rules allow or restrict traffic within subnets.

- **Cloud Interconnect:** Extends on-premises network to GCP via dedicated or partner interconnect.
- **Cloud VPN:** Connect on-premise environment to GCP securely over the ISP infrastructure through IPSec VPN (more affordable)
- **Peering:** Enables direct access to Google Cloud resources with reduced internet egress fee (bandwidth that you are charged for outbound interconnectivity)

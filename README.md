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

# Design - Platform to Support Microservice Applications.

## Infrastructure Platform:
This design proposes to use the Red Hat OpenShift Container Platform deployed on Bare Matal Servers as the Infrastructure platform to support the Microsevice Applications. The infrastructure is built on-premises within a fully air-gapped environment.

Kubernetes is the popular and widely adopted purpose build platform to run the microservices application, as the organization is embracing the cloud native architecuture Kubernetes is a natural choice. We have the option to use upstream Kubernetes or an enterprise distribution. We decided to go with the enterprise distribution - Red Hat OpenShift Container Platform.

Red Hat OpenShift Container Platform:
* Enterprise distribution will have the lower TCO in long term operations in enterprise environments.
* Popularity in enterprise environements and huge ecosystem support.

The solution is designed as a single site solution without any single point of failure, and doesnot consider multi-zone or mutli-site cluster topology.

### Network Connectivity
![Network Connectivity](https://github.com/user-attachments/assets/a228889f-d3ed-4aca-9025-02e536286dee)

### Cluster Installation
Various methods are available for deploying the Red Hat OpenShift Container Platform on Bare Metal server, we decided to use the UPI deployment methodology as this option provides us with the full customization capabilities during the deployment.

Initially the bare metal server will be deployed and configured manually and the RHCOS will be installed using the ISO boot and coreos-installer, once the cluster deployment is completed the bare metal management can be automated using the BareMetal Operator. We are not anticipating the bare metal management to be highly dynamic, so setting up the PXE boot infrastruture is not considered.

### Cluster Configuration Management
OpenShift Gitops will be used for the cluster configuration management. Gitops workflow will be used to decleratively manage the cluster configuration. Git repositry will be the source of truth of the cluster configuration.

### Components

#### Storage

##### Block Storage
Dell PowerFlex storage array is selected as the block storage platform. The Dell PowerFlex will be integrated with the OpenShit cluster using the Dell Container Storage Modules. The deployement and management of csm can done using the operator and the dell csm operator is available in the OpenShift Operator Hub.
Dell PowerFlex is a IP Storage, and the storage connectivity is provided using two isolated network for redundancy, and all the worked nodes must be connected to these storage networks.
Dell PowerFlex is selected:
* IP Based Storage
* Scaleout architecture, massively scalable storage array to support the cloud native workloads.
* Lifecycle of the Storage array independant of the OpenShift Cluster.

#### Object Storage
Dell ObjectScale is selected as the object storage platform.
Dell ObjectScale is selected:
* Scaleout architecture, massively scalable storage array which can support the cloud native workloads.
* Lifecycle of the storage array independant of the OpenShift cluster.

#### File Storage
The file storage is not considerd in this design, but can be later integration in to the environment.


### Image Registry:

#### Internal Registry
OpenShift cluster has a internal registry which required for the normal operation of the cluster. The internal registry must be configured with persistent storage for persistently storing the container images. We decided to configure the internal image registry to use object storage, S3 bucket configured in the Dell ObjecctScale.

#### External Registry
Red Hat Quay has been chosen as the external image registry, and it will be deployed on the OpenShift cluster using an S3 bucket from Dell ObjectScale as its storage backend. 

#### Mirror Registry
Local image registry is required for the deployment of OpenShift cluster in an air-gapped environment, a temporary mirror registry will be deployed to support the inital openshift cluster deployment. All the container images required for the installation of the openshift cluster is synced to the mirror registry using the oc-mirror utility.

### Monitoring 
Red Hat OpenShift Monitoring will be configured for both the cluster monitoring and the user workload monitoring. For every application which exposes the metrices can be scraped by the user workload monitoring prometheus instance running in the openshift cluster. The metrics then can be monitored from the OpenShift console, if required custom dasboards can be created and added to the openshift console. Eventhough not considerd in the design, it is possible to connect grafana with the openshift monitoring stack for visualizing the metices.

### Logging
Red Hat OpenShift cluster logging will be configured to forward the audit, infrastructure and application logs to a remote syslog server.


### Authentication
Oepnshift cluster will be integrated with the active directory using ldaps as the external identity provider. Active directory security groups will be used to grant the access control.

### Valut Server:
The design proposes the use of vault server to keep all the secrets, however the design does not incude the procedure for the deployment and integration of vault server and the secrets manifest files are pushed to the git repostory, which is done only to demonstrate the application deployment and must not be done in production.

### Disaster Recovery:
The design doesnot consider the disaster recovery and business continuity requirements, however the design can be extended to include disaster recovery soultion.

### Backup and Recovery:
The microservice application has persistent data in the database, the design includes a backup and recovery solution to protect the persistent data. The design proposes Dell PowerProtect Manager as the Backup Storage and Dell PowerProtect DataDomain as the Backup Storage, however didnt included the configuration and integration of the backup solution with the openshift cluster.

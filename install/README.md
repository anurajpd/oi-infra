# Red Hat OpenShift Container Platform Bare Metal Installation

## Prerequisite

* Network Configuration - Port Confiuration, Firewall Configuration.
* Load Balancer Configuration - API and Ingress endpoint.
* DNS Configuration - A and PTR record for all the nodes, api and ingress.
* DHCP Configuration - Persistent IP reservation for all the nodes.
* HTTP Repository.
* NTP Server.
* Certificates for API and Ingress.
* Bastion server with all the required tools and installation packages.
* Mirror registry is installed and configured.
* Git repository with all the required openshift configurations.

## Deployment Procedure

1. Download the OpenShift release packages in a machine with internet access using the oc-mirror utility.

2. Move the openshift release packages to the bastion host.

3. Push the OpenShift release packages to the mirror registry.

4. Create the install-config.yaml file with the required installtion configuration.

5. Generate the openshift manifest file from the install-config.yaml using the openshift-install utility.

6. Generate the ignition file from the manifest file using the openshift-install utility.

7. Upload the ignition file to the HTTP repository.

8. One of the compute node will be used as temporary bootstrap node. Boot the nodes from the RHOCS boot images.

9. Install the RHOCS on to the node with the required customizations including the nework configurations using the coreos-installer.

10. Reboot the node to start the openshift cluster installations.

11. Verify the cluster installation is completed using the openshift-install utility.

12. Configure the IDMS, ITMS and the catalog sources.

13. Install and configure the openshift-gitops.

14. Configure the argocd instance to connect to the git repository containes the openshift configurations.

15. Conduct UAT test before releasing the infrastructure for production.

# govc-openshift4-vmware

Deploy OpenShift 4.3 and later using static IP addresses for master and worker nodes.  It will upload the OpenShift Bare Metal BIOS file and ignition files to a websever, and will create a bootable ISO for each node with static IP configuration and ignition parameters already set.

## Prereqs

1. [DNS](https://docs.openshift.com/container-platform/4.3/installing/installing_vsphere/installing-vsphere.html#installation-dns-user-infra_installing-vsphere) needs to be configured ahead of time
2. [Loadbalancers](https://docs.openshift.com/container-platform/4.3/installing/installing_vsphere/installing-vsphere.html#installation-network-user-infra_installing-vsphere) need to be configured ahead of time
3. An existing webserver that can serve files to the OpenShift nodes for ignition.

## Installation Process

```bash
$ git clone https://github.com/ncolon/govc-openshift4-vmware
$ cd govc-openshift4-vmware
$ cp environemnt.properties.example environment.properties
```

Update your environment.properties with your environment values and run `install.sh`

```bash
$ ./install.sh
Downloading GOVC
Downloading OpenShift ISO
Downloading OpenShift BIOS
Downloading OpenShift Installer
Downloading OpenShift Client
Mounting ISO
mount: /dev/loop0 is write-protected, mounting read-only
Copying ISO files
Unmounting ISO
Creating bootable ISO for mycluster-bootstrap-1
Creating bootable ISO for mycluster-master-1
Creating bootable ISO for mycluster-master-2
Creating bootable ISO for mycluster-master-3
Creating bootable ISO for mycluster-worker-1
Creating bootable ISO for mycluster-worker-2
Creating bootable ISO for mycluster-worker-3
Creating bootable ISO for mycluster-worker-4
Creating bootable ISO for mycluster-worker-5
Creating bootable ISO for mycluster-worker-6
Creating bootable ISO for mycluster-worker-7
Creating bootable ISO for mycluster-worker-8
INFO Consuming Install Config from target directory
Powering on VirtualMachine:vm-1086... OK
Powering on VirtualMachine:vm-1087... OK
Powering on VirtualMachine:vm-1088... OK
Powering on VirtualMachine:vm-1089... OK
Powering on VirtualMachine:vm-1090... OK
Powering on VirtualMachine:vm-1091... OK
Powering on VirtualMachine:vm-1092... OK
Powering on VirtualMachine:vm-1093... OK
Powering on VirtualMachine:vm-1094... OK
Powering on VirtualMachine:vm-1095... OK
Powering on VirtualMachine:vm-1096... OK
Powering on VirtualMachine:vm-1097... OK
INFO Waiting up to 30m0s for the Kubernetes API at https://api.mycluster.example.com:6443...
INFO API v1.16.2 up
INFO Waiting up to 30m0s for bootstrapping to complete...
INFO It is now safe to remove the bootstrap resources
INFO Waiting up to 30m0s for the cluster at https://api.mycluster.example.com:6443 to initialize...
INFO Waiting up to 10m0s for the openshift-console route to be created...
INFO Install complete!
INFO To access the cluster as the system:admin user when using 'oc', run 'export KUBECONFIG=/home/virtuser/govc-openshift4-vmware/mycluster/auth/kubeconfig'
INFO Access the OpenShift web-console here: https://console-openshift-console.apps.mycluster.example.com
INFO Login to the console with user: kubeadmin, password: XXXX-XXXX-XXXX-XXXX
```

## environment.properties variables

| Variable                     | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| VSPHERE_HOSTNAME             | FQDN or IP Address of your vSphere Server                    |
| VSPHERE_USERNAME             | vSphere username                                             |
| VSPHERE_PASSWORD             | vSphere password                                             |
| VSPHERE_INSECURE             | Allow vSphere self-signed certs                              |
| VSPHERE_DATACENTER           | vSphere Datacenter where OpenShift will be deployed          |
| VSPHERE_IMAGE_DATASTORE      | vSphere Datastore where bootable ISOS will be stored         |
| VSPHERE_IMAGE_DATASTORE_PATH | vSphere Datastore path where bootable isos will be stored    |
| VSPHERE_NODE_DATASTORE       | vSphere Datastore for OpenShift nodes                        |
| VSPHERE_NETWORK              | vSphere Network for OpenShift nodes                          |
| BINARIES_GOVC                | URL for GOVC binary                                          |
| BINARIES_ISO                 | URL for OpenShift ISO                                        |
| BINARIES_BIOS                | URL for OpenShift BIOS                                       |
| BINARIES_INSTALLER           | URL for OpenShift Installer                                  |
| BINARIES_CLIENT              | URL for OpenShift Client                                     |
| WEBSERVER_EXTERNAL_IP        | External IP Address of webserver.  Ignition and BIOS will be uploaded to this IP Address |
| WEBSERVER_INTERNAL_IP        | Internal IP Address of webserver.  Nodes will use this IP Address to pull ignition config files. |
| WEBSERVER_PORT               | HTTP Port WebServer is listening on                          |
| WEBSERVER_USERNAME           | WebServer username                                           |
| WEBSERVER_SSHKEY             | SSH Key for WebServer username                               |
| WEBSERVER_WEBROOT            | WebRoot for your WebServer.                                  |
| OPENSHIFT_BASEDOMAIN         | Base domain for your OpenShift Cluster                       |
| OPENSHIFT_CLUSTERNAME        | Name of your OpenShift cluster.  Cluster will be reachable at `api.$OPENSHFIT_CLUSTERNAME.$OPENSHIFT_BASEDOMAIN`. |
| OPENSHIFT_PULLSECRET         | Path to your OpenShift pull secret.  Download from https://cloud.redhat.com/openshift/install/vsphere/user-provisioned |
| OPENSHIFT_MACHINECIDR        | The IP address pools for machines.                           |
| OPENSHIFT_CLUSTERCIDR        | The IP address pools for pods. The default is 10.128.0.0/14  |
| OPENSHIFT_SERVICECIDR        | CIDR for services in the OpenShift SDN                       |
| OPENSHIFT_HOSTPREFIX         | The prefix size to allocate to each node from the CIDR. For example, 24 would allocate 2^8=256 adresses to each node. |
| NETWORK_GATEWAY              | Network Gateway for VMs                                      |
| NETWORK_NETMASK              | Network Mask for VMs                                         |
| NETWORK_NAMESERVERS          | Nameservers for VMs.                                         |
| NETWORK_DEVICE               | Device name for your VMs.  In 4.2 its `ens192`, in 4.3 its `ens32`. |
| BOOTSTRAP_IPADDRESS          | IP Address for Bootstrap node                                |
| BOOTSTRAP_HOSTNAME           | Hostname for Bootstrap node                                  |
| BOOTSTRAP_CPU                | number of CPUs for Bootstrap node                            |
| BOOTSTRAP_MEM                | memory in GBs for Bootstrap node                             |
| BOOTSTRAP_DISK               | OS Disk storage for Bootstrap node                           |
| MASTER_COUNT                 | number of control-plane nodes                                |
| MASTER_IPADDRESS             | Array of IP Addresses for control-plane nodes                |
| MASTER_HOSTNAME              | Array of Hostnames for control-plane nodes                   |
| MASTER_CPU                   | number of CPUs for control-plane nodes                       |
| MASTER_MEM                   | memory in GBs for control-plane nodes                        |
| MASTER_DISK                  | OS Disk storage for control-plane nodes                      |
| WORKER_COUNT                 | number of worker nodes                                       |
| WORKER_IPADDRESS             | Array of IP Addresses for worker nodes                       |
| WORKER_HOSTNAME              | Array of hostnames for worker nodes                          |
| WORKER_CPU                   | number of CPUs for worker nodes                              |
| WORKER_MEM                   | memory in GBs for worker nodes                               |
| WORKER_DISK                  | OS Disk storage for worker nodes                             |
| STORAGE_COUNT                | number of storage nodes for OpenShift Container Storage.  Defaults to 0. |
| STORAGE_IPADDRESS            | Array of IP Addresses for storage nodes                      |
| STORAGE_HOSTNAME             | Array of hostnames for storage nodes                         |
| STORAGE_CPU                  | number of CPUs for storage nodes.  OCS requires 16 CPUs or more. |
| STORAGE_MEM                  | memory in GBs for storage nodes.  OCS requires 64 GB or more. |
| STORAGE_DISK                 | OS Disk for storage nodes                                    |
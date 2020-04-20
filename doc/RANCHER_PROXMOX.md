Deploy FADI with rancher and proxmox
=============

* [1. Upload ISO on Proxmox Node](#1-Upload-IS0-on-Proxmox-Node)
* [2. Install Rancher](#2-Install-Rancher)
* [3. Add docker-machine driver](#1-Add-docker-machine-driver)
* [4. Create Cluster With Rancher](#2-Create-Cluster-With-Rancher)
     * [Create Node Template](#Create-Node-Template)
     * [Create Cluster](#Create-Cluster)
     * [Create The Nodes](#Create-The-Nodes)
* [5.Manage the provisioning of the persistent volumes](#5-Manage-the-provisioning-of-the-persistent-volumes)
* [5. Control Cluster from Local PC](#3-Control-Cluster-from-Local-PC)

This page provides informations on how to create a Kubernetes cluster and how to deploy FADI using [Rancher](https://rancher.com/) and [Proxmox](https://www.proxmox.com/en/).

## 1. Upload ISO on Proxmox Node

<a href="https://www.proxmox.com/" alt="ProxMox"> <img src="images/logos/Proxmox.png" width="150px" /></a>

> "Proxmox VE is a complete open-source platform for enterprise virtualization. With the built-in web interface you can easily manage VMs and containers, software-defined storage and networking, high-availability clustering, and multiple out-of-the-box tools on a single solution."

First you need to download the iso rancheros-proxmoxve-autoformat.iso, you can download it by clicking [here](https://github.com/rancher/os/releases/download/v1.5.5/rancheros-proxmoxve-autoformat.iso).

Once downloaded, you need to upload this iso on your proxmox node.

## 2. Install Rancher

We consider in this documentation that you already have deployed Rancher. However, we give you the instructions that we have followed to deploy our Rancher server: [https://rancher.com/docs/rancher/v2.x/en/installation/other-installation-methods/single-node-docker/advanced/](https://rancher.com/docs/rancher/v2.x/en/installation/other-installation-methods/single-node-docker/advanced/).

## 3. Add docker-machine driver

Then, you need to allow Rancher to access Proxmox. We have contributed to upgrade a existing [docker-machine driver](https://github.com/lnxbil/docker-machine-driver-proxmox-ve/releases/download/v3/docker-machine-driver-proxmoxve.linux-amd64
) to make it compatible with Rancher.

To add this driver in your Rancher, follow these steps :

![Proxmox driver](images/installation/proxmoxdriver.gif)

Driver Url:
```
https://github.com/lnxbil/docker-machine-driver-proxmox-ve/releases/download/v3/docker-machine-driver-proxmoxve.linux-amd64
```

## 4. Create Cluster With Rancher

<a href="https://rancher.com/" alt="Rancher"> <img src="images/logos/rancher.png" width="150px" /></a>

> "Rancher is open source software that combines everything an organization needs to adopt and run containers in production. Built on Kubernetes, Rancher makes it easy for DevOps teams to test, deploy and manage their applications."

After connecting to rancher, you can follow these steps:

### Create Node Template

This is where you have to define the templates you want to use for your nodes (both masters and workers nodes). To do so, you can go to: `profile (top right corner)`  > `Node templates` > `Add Template` :

Choose `Proxmoxve`
![Proxmoxve](images/installation/Proxmoxve.png)
and then fill the rest of the fields like the IP of the Proxmox `i.e. proxmoxHost`, the username/password `i.e. proxmoxUserName, proxmoxUserPassword `, storage of the image file `vmImageFile ` which is in our case `local:iso/rancheros-proxmoxve-autoformat.isot` and coming down to the resources you want to allocate for your node `i.e. nodevmCpuCores, vmMemory, vmStorageSize `.

### Create Cluster

To create your cluster:

 `Cluster`  > `Add Cluster` > `Proxmoxve`

you will need to give a name to your cluster, then precise the nodes in the cluster, at first start with **one master node**, you give it a name, choose of the templates created earlier for that node and then tick all 3 boxes for `etcd`, `Control Plane` and `Worker`, then choose the kubernetes version and click `create`.

> "you'll have to wait the `VM creation`, `the RancherOS install` and `the IP address retrieving` and that might take a while "

 Once the master node gets its IP address, go to `Cluster`  > `Edit Cluster` and add an other worker node, untick the worker box from the master node and tick it in the new worker node. It should look to something like this:
 ![Proxmoxve](images/installation/workernode.png)

If a second (or more) node (master or worker) is needed, you can either add an other one with a different template by following the same way we just did. You can also add as much nodes as you want using the same template by simply going to  `YourCluster (not global)`  > `nodes` > `+` and it will add an other node of the same kind:

 ![Proxmoxve](images/installation/addnode.png)

## 5. Manage the provisioning of the persistent volumes.

#### StorageOS

Once all your nodes are up and running, it's time to deploy your services. But before, you need to set your default PVC for the persistent volumes. To do so, we first need to deploy the volume plugin `StorageOS`, go to `YourCluster (not global)`  > `system` > `apps` > `launch` and search for `StorageOS`. Make sure all the fields are filled correctly, like the following screenshot:

![Proxmoxve](images/installation/StorageOS.png)

and now, launch it 🚀.

> "launching apps usually takes several minutes, you're going to need to wait a few minutes till the "

Be Careful that this service gives the posibility to allocate maximum 50Gi with the basic License.

![Proxmoxve](images/installation/StorageOS_limits.png)
#### Manualy

TBT

### Deploy FADI
![defaultpvc](images/installation/defaultpvc.png)

 selector set as default the StorageClass
#### Longhorn
7. edit the values yaml of fadi
8. run fadi

## 4. Control Cluster from Local PC
---
type: docs
title: "Azure IoT Operations - Edge/MEC Accelerator Application"
linkTitle: "Azure IoT Operations - MEC Accelerator Application"
weight: 3
description: >
---

# Create live video analytics at the edge/MEC using Azure IoT Operations (AIO)

"Contoso Energy", a fictional company, requires a live video analytics at the edge (in its plants) with alerts dashboards and dynamic provisioning of any number of IP video cameras at the MEC (Multi-access Edge Compute through 5G, Wi-Fi or Ethernet) to provide real-time alerts on issues detected by AI models analyzing the video streams.

In a Private MEC (Multi-access Edge Compute) that needs to cover large outdoor/indor industrial areas, the best fit is to use a private 5G network with 5G video cameras. However, in other use cases Wi-Fi or Ethernet networks can also be used instead of a 5G network. 

## Goals for the 'MEC app solution accelerator' (This example application)

As mentioned, the network needs are provided by the infrastructure such as a 5G network or Wi-Fi and eEdge compute. 

However, aspects such as being able to create an event-driven application (Devices-->AI-->Events-->Logic-->Alerts-->Handlers) need to be implemented by your application and it's precisely the value that this example application provides:

- Recommendations on using event-driven and microservices architecture explained by this example implementation.
- Showcase of design patterns to implement (Event Pub/Sub, microservices autonomy, extensible events/alerts metadata schemas, etc.).
- Show how to inference with deep-learning models from a microservice, in this case using 'Yolo', for video/image analytics, then generate the related detection event.

## High level modules architecture

The solution involves the following high level modules or related applications:

- A control-plane application in charge of:
  - Dynamically provision video cameras (n cameras).
  - (Optional) Dynamically provision of related 5G SIMs, if using a 5G network with Azure Private 5G Core.

- A video analytics and event-driven alerts system in charge of:
  - Scalable video stream ingestion (n streams).
  - Scalable object/issue detection based on an AI model.
  - Alert rules engine to determine if an event should be really an alert.
  - Alerts dashboard UI.

  Below you can see a high-level diagram with the above modules and interaction:

![Screenshot showing high level modules architecture diagram](./docs/imgs/arch_01.png)

You can see how the control plane app drives the actions related to provisioning assets such as cameras or SIMs (dotted lines in yellow), while the 'Alert app' is the live system consuming the video streams and detecting issues/alerts with the AI model (lines in white).

## Internal software architecture of the MEC Aplication Solution Accelerator

The internal software architecture (event-driven and microservice oriented architecture) and development technology details are explained in detailed in this page:

[MEC-Accelerator Internal Software Architecture details](./docs/_architecture_mec_accelerator.md)

## Functional features of the application

You can get an overview of this application's functionality by watching the following video:

[![Watch the app intro video](https://img.youtube.com/vi/sA5mIU9ymb8/hqdefault.jpg)](https://www.youtube.com/embed/sA5mIU9ymb8)

[<img src="https://img.youtube.com/vi/FaV0tIaWWEg/hqdefault.jpg" width="1024" height="768"
/>](https://www.youtube.com/embed/FaV0tIaWWEg)


Once the application is deployed into Kubernetes, as mentioned in the high-level modules architecture, there are two main work areas, showcased below:

### 1. Control Plane app UI

The home page of this app basically shows you the main actions you can do from here:

<img width="1000" alt="Control Plane app home page" src="./docs/imgs/deploy_02.png">

#### (OPTIONAL) 5G SIMs provisioning

As mentioned, this configuration is optional. If you are not using any cellular 5G network, you can also use Wi-Fi or ethernet, as alternatives.

When you click on the 'SIMs Provisioning' menu option, the application will show you the list of SIMs already provisioned in your Azure Private 5G Core network. That information is coming directly from Azure Private 5G Core thorugh its REST APIs.

<img width="1000" alt="image" src="./docs/imgs/ui_features_01.png">

The value-added provided by this UI integrated to Azure Private 5G Core is that any regular business application operator can provisiong SIMs without having access to the Azure subscription/portal as an administrator or technical person.

User operators can easily delete or add new SIM cards to the cellular 5G network by providing the typical SIM data:

<img width="1000" alt="image" src="./docs/imgs/ui_features_02.png">

It's very straightforward because even the IP to be provided comes from the a query to AP5GC with a pool of available IPs. Also the available SIM groups and SIM Policies to assign to.

#### IP video cameras dynamic provisioning

The most important asset to provision are te video cameras. This application allows to dynamically provision 'n' number of IP cameras without having to update any code or configuration settings. A user can directly do it from the UI and the pods in Kubernetes will dynamically support the new stream ingestion, as in the following screenshot:

<img width="1000" alt="image" src="./docs/imgs/ui_features_03.png">

The important camera's value in there is the RTSP Uri. The rest of the values are simply to make it easier to construct the Uri, but if you know the Uri of your IP camera, you can also directly provide it, including Wi-Fi or Ethernet IP cameras, not just 5G cellular cameras.

Once you add any camera to the system you can check that it's working in the 'Cameras dashboard' page:

<img width="1000" alt="image" src="./docs/imgs/ui_features_04.png">

### 2. Alerts dashboard app UI

Finally, but as the most important feature of this application, you can see the alerts being triggered originally detected by the AI model analyzing the video, as in the below screenshot which is detecting a person and showing that fact within a bounding-box:

<img width="1000" alt="image" src="./docs/imgs/ui_features_05.png">

Since these alerts are internally defined as messages going through a MQTT broker following a Publish/Subscription pattern, you could easily extendthis application and propagate these alerts as emails, text messages or to any other business system related to alerts.

## Getting started: Deploy the MEC Application Solution Accelerator into a light Kubernetes cluster at the edge 

## Supported environment infrastructure at the edge

This version of MEC-App-Accelerator supports the following Kubernetes distributions and host operating system.

- **AKS Edge Essentials on Windows**
- **K3s on Ubuntu Linux**

Additionally and on top of the Kubernetes/k3s cluster, you need to install **Azure IoT Operations**, related to the following assets:

  - IoT MQ as messaging broker
  - AKRI for dynamic cameras provissioning (Curently using the AKRI OSS version).

## Install the Kubernetes/K3s cluster at the edge

Install the AKS-EE-Windows cluster or the K3s-Ubuntu cluster by following the instructions in the below Microsoft official doc.

**Install AKS-EE-Windows alternative:** 

[Create your Azure Arc-enabled Kubernetes cluster on Windows](https://learn.microsoft.com/en-us/azure/iot-operations/deploy-iot-ops/howto-prepare-cluster?tabs=aks-edge-essentials)

**Install K3s-Ubuntu-Linux alternative:** 

[Create your Azure Arc-enabled K3s cluster on Ubuntu-Linux](https://learn.microsoft.com/en-us/azure/iot-operations/get-started/quickstart-deploy?tabs=linux#connect-a-kubernetes-cluster-to-azure-arc)



## Install Azure IoT Operations into your cluster

### Prerequisites for Azure IoT Operations

It's important to check and install the prerequisites for Azure IoT Operations explained here:

[Prerequisites for Azure IoT Operations](https://learn.microsoft.com/en-us/azure/iot-operations/get-started/quickstart-deploy?tabs=windows#prerequisites)

### Deploy Azure IoT Operations

Install Azure IoT Operations by following the official "simplified/automated approach" documentation here:

[Deploy Azure IoT Operations Preview](https://learn.microsoft.com/en-us/azure/iot-operations/get-started/quickstart-deploy?tabs=windows#deploy-azure-iot-operations-preview)



## Deploy the MEC-Application into the cluster at the edge

There are a few prerequisites and steps for the application itself, explained in the following sections.

### Clone the MEC-Accelerator GitHub repo

You need to clone the MEC-Accelerator GitHub repo in the client machine from where you will deploy the application against your Kubernetes/k3s cluster.

Basically, you need to have the cloned repo in the same machine from where you also use KUBECTL against your K8s/K3s cluster.

Logically, you need to have [installed Git](https://git-scm.com/downloads) in the first place.

Then, using a command-line tool (PowerShell in Windows or Bash in Linux) clone the repo:

```powershell
git clone https://github.com/Azure/mec-app-solution-accelerator.git
```

**TEMPORAL NOTE:** Until v2 is in the **main branch**, please use the branch **"mec-accelerator-aio-dev"** by positioning within the folder of the cloned repo and running this command:

```powershell
git checkout mec-accelerator-aio-dev
```

### Move to the deployment folder

From the console, move to the deployment folder within the cloned repo:

```powershell
cd <YOUR PATH>/deploy/k8s
```

### (OPTIONAL) Update the application control plane configuration for accesing your Azure Private 5G network

Update the config map that contains essential information for connecting to our Azure Mobile Network. You'll find the configuration in the file `deploy/k8s/12-control-plane-api-config-map.yaml`.

Replace the following values in the file:

```yaml
  MobileNetwork__SubscriptionId: "your_subscription_id"
  MobileNetwork__ResourceGroup: "your_resource_group"
  MobileNetwork__Name: "your_network_name"
  MobileNetwork__AttachedDataNetwork: "your_attachedDataNetwork_fullID"
  MobileNetwork__Slice: "your_slice_fullID"
  ClientCredentials__TenantId: "your_tenant_id"
  ClientCredentials__ClientId: "your_client_id"
  ClientCredentials__ClientSecret: "your_client_secret"
```

*Note:* This step is required if you want to manage SIMs provisioning from this app against AP5GC (Azure Private 5G Core) in Azure. 
For just getting alerts and camera management/provisioning (i.e. Wi-Fi or Ethernet cameras provisioning), you can skip this part if you don't have an AP5GC Mobile Network and packet core deployed into an ASE, RAN, 5G UEs, etc.

### Check your kubectl context

Make sure that your default kubectl context is pointing to the right cluster: 

  Open a new command-shell and run the following command to check your current contexts:

  ```powershell
  kubectl config get-contexts
  ```

  To set the context to point to the right Kuberentes cluster, use kubectl and provide the name of the context to use:

  ```powershell
  kubectl config use-context [my-cluster-context]
  ```


### Run the application deployment script

Within the `deploy/k8s` folder there are two Script files, one for Windows and one for Linux that handle the application  deployment into the cluster, including dependencies such as Dapr and AKRI.

The script will run the following deployment steps:

- Install Dapr and Helm.
- Initialize mec-accelerator namespace in the cluster where all the resources will be deployed.
- Install Akri with the specific version depending on the Kubernetes distro used (k8s/k3s).
- Deploy the selected MQTT broker.
- Deploy the rest of the accelerator resources (microservices pods).

The script needs two arguments to be provided depending on the cluster type and the MQTT broker selected:

- **Kubernetes distro:** The `kubernetesDistro` parameter supports Kubernetes and K3s. Supported choice arguments are `k8s` and `k3s`.

- **MQTT broker:** The `mqttBroker`  parameter supports Azure IoT MQ broker (aka. E4K) and Mosquitto. Supported choice arguments are `E4K` and `mosquitto`.

Note that if using Azure IoT MQ broker, **Azure Arc** and **Azure IoT Operations** must be installed in the cluster based on the provided initial instructions above.

**IMPORTANT:** The application setup script has to be executed on the same machine/environment where you would typically run KUBECTL against your cluster, so it can be on the same cluster's machine (i.e. where you have installed AKS Edge Essentials) or on any client machine able to use KUBECTL against your k3s/Ubuntu-VM cluster.

#### OPTION A: Execute the script in Windows

Execute the script `deploy-accelerator.ps1` while providing the selected parameters:

For deploying into AKS Edge Essentials or a plain K3S in an Ubuntu-Linux, with Azure IoT MQ (aka.E4K), the command is the following:

```powershell
.\deploy-accelerator.ps1 -kubernetesDistro k3s -mqttBroker E4K
```


#### OPTION B: Execute the script in Linux

Execute the script `deploy-accelerator.sh` while providing the selected parameters: 

For deploying into AKS Edge Essentials or a plain K3S in an Ubuntu-Linux, with Azure IoT MQ (aka.E4K), the command is the following:

```powershell
./deploy-accelerator.sh --kubernetesDistro k3s --mqttBroker E4K
```

Basically, the only difference in usage should be the script file name (**.ps1** vs. **.sh**).

Here's an example of the application's setup script execution:

<img width="1000" alt="image" src="./docs/imgs/deploy_00.png">

### Try the 'Control Plain app' to provision cameras

In order to know the URL for the 'Control Plain web app' (IP and port to use), type the following command:

```powershell
kubectl get services --namespace mec-accelerator
```

<img width="1000" alt="image" src="./docs/imgs/deploy_06.png">

Then search for the service with name **"control-plane-ui-service"** and related IP and external port, so you write a URL similar to the following in a browser:

`http://<your-IP>:90/`

<img width="1000" alt="image" src="./docs/imgs/deploy_02.png">


### Access the Alerts dashboard UI with Alerts originated from AI model detections

To access the  front-end, 
In order to know the URL for the 'Alerts dashboard UI' front-end (IP and port to use), type the following command:

```powershell
kubectl get services --namespace mec-accelerator
```

<img width="1000" alt="image" src="./docs/imgs/deploy_07.png">

Then search for the service with name **"alerts-ui"** and related IP and external port, so you write a URL similar to the following in a browser:

`http://<your-IP>:88/`

Alternatively, you can also click on the link "Alerts Dashboard" from the Control-Plane app here:

<img width="1000" alt="image" src="./docs/imgs/deploy_03.png">

Following any of those ways you should be able to run the 'Alerts dashboard UI' and check out the Alerts originated from the AI models when analyzing the video:

<img width="1000" alt="image" src="./docs/imgs/deploy_05.png">


### Remove the MEC-Application from Kubernetes 

- Open a new command-shell and change the current folder to the `deploy/k8s` folder of this repo.

- Run the `deploy-accelerator` script with the **uninstall** parameter to remove all related application's resources from Kubernetes.

Windows:

```powershell
.\deploy-accelerator.ps1 -uninstall
```

Linux:

```powershell
.\deploy-accelerator.sh -- uninstall
```



# Table of Contents
- [Introduction](https://github.com/eu-NebulOuS/sal/blob/main/README.md#introduction)
- [Report Issue](https://github.com/eu-nebulous/sal/blob/main/README.md#report-issue)
- [NebulOuS Development](https://github.com/eu-nebulous/sal/blob/main/README.md#nebulous-development)
- [NebulOus scenario](https://github.com/eu-nebulous/sal/blob/main/README.md#nebulous-scenario)
	- [1. Prerequisites](https://github.com/eu-nebulous/sal/blob/main/README.md#1-prerequisites)
	- [2. Cloud registration](https://github.com/eu-nebulous/sal/blob/main/README.md#2-cloud-registration)
	- [3. Edge device registration](https://github.com/eu-nebulous/sal/blob/main/README.md#3-edge-device-registration)
	- [4. Filtering of node candidates](https://github.com/eu-nebulous/sal/blob/main/README.md#4-filtering-of-node-candidates)
	- [5. Cluster deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#5-cluster-deployment)
	- [6. Application management](https://github.com/eu-nebulous/sal/blob/main/README.md#6-application-management)
	- [7. Cluster reconfiguration](https://github.com/eu-nebulous/sal/blob/main/README.md#7-cluster-reconfiguration)
	- [8. Edge device deregistration](https://github.com/eu-nebulous/sal/blob/main/README.md#8-edge-device-deregistration)
	- [9. Cloud deregistration](https://github.com/eu-nebulous/sal/blob/main/README.md#9-cloud-deregistration)
 	- [10. SAL Persistance for developers](https://github.com/eu-nebulous/sal#10-sal-persistance-for-developers-and-project-mentors)	
- [NebulOuS SAL Deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#nebulous-sal-deployment-managed-by-7bulls)

# Introduction
Deployment Manager, i.e. Scheduling Abstraction Layer (SAL) is an abstraction layer initially developed as part of the EU project Morphemic by [Activeeon](https://www.activeeon.com/). Its development continued through the NebulOuS EU project. SAL aims to enhance the usability of Execution Adapter, i.e. [ProActive Scheduler & Resource Manager](https://doc.activeeon.com/latest/), by providing abstraction, making it easier for users to interact with the scheduler and take advantage of its features. Seamlessly supporting REST calls and direct communication with the Execution Adapter SAL empowers users to harness the scheduler's capabilities. 

SAL Code repository and documentation can be found [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md).

# Report Issue
In a case of issue, please create the bug error report [here](https://github.com/eu-nebulous/sal/issues). 
When reporting issue, for faster resolution of your problem, please include:
- the description of the scenario e.g. NebulOuS sequence diagrams which were executed
- date and time, SAL and ProActive environment
- SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (especially ones inside of container)
- ProActive [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive) i.e. `connector-iaas.log`
- ProActive job id (in a case of error during the ProActive workflow execution)
- detailed description of the action during which it happened 

# NebulOuS Development
Note that there is additional documentation for NebulOuS development is provided [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/deployment-manager-sal-1).
For preset NebulOuS environment for testing and development, you can find more information on how to access SAL [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/sal-in-nebulous-k8s), and regarding ProActive [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/proactive-in-nebulous-k8s) 

# NebulOus scenario 
This section describes how the Deployment Manager and Execution Adapter support the NebulOuS scenario. It outlines the sequence of SAL operations provided to facilitate NebulOuS deployment and execution. For more information on using SAL endpoints, refer to the [SAL Endpoint Documentation](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

Developers can utilize the provided [Postman collection](https://github.com/eu-nebulous/sal/blob/main/resources/SAL%20for%20Nebulous.postman_collection.json) to get started with the endpoints or consult the previous [documentation](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/sal-postman-testing-scenario) for further details on the testing scenario.

### 1. Prerequisites
To use SAL, you must have the Execution Adapter (ProActive) [installed](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#proactive-scheduler) and [properly configured](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#configure-proactive-scheduler-details).
In the [configuration script](https://github.com/eu-nebulous/sal/blob/main/resources/deployment.yaml), it is necessary only to set 
- `<PROACTIVE_URL>`
- `<USERNAME>`
- `<PASSWORD>`

The rest of the configuration is automatically handled by NebulOuS (see [NebulOuS SAL deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#nebulous-sal-deployment-managed-by-7bulls) for more details).

For additional information on setting up the SAL Kubernetes deployment script, refer to [this guide](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#23-deploying-sal-as-a-kubernetes-pod). You can find details on using the endpoints [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

#####  1.1. [Connect endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/1-connection-endpoints.md#11--connect-endpoint) - Establishing the connection to ProActive server. 
SAL must be connected to ProActive to use any of the endpoints. If you encounter an `HTTP 500` when calling endpoints, which reports a `NotConnectedException`, it indicates that SAL is not connected to ProActive. You can verify this in the SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (particularly those within the container).
Keep in mind that the connection to ProActive may be lost during scenario execution and may need to be reestablished.

### 2. Cloud registration
#####  2.1. [Add cloud endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md) - Defining a cloud infrastructure.
To use this endpoint, you must specify a unique `cloud_name` that has not already been registered. Note that after a SAL restart, cloud information is erased from the SAL database, though it remains in the Execution Adapter. If you use a `cloud_name` that has already been registered, the infrastructure will not be updated with new information, and resources on the cloud provider may not be properly released. The only proper way to remove cloud resources is by using the [Cloud deregistration](https://github.com/eu-nebulous/sal/blob/main/README.md#9-cloud-deregistration)  endpoint.

For more information on setting up cloud providers for NebulOuS, refer to the [Managing Cloud Providers](https://github.com/eu-nebulous/nebulous/wiki/2.1-Managing-cloud-providers) documentation.

Additionally, while the infrastructure may appear registered, this does not guarantee the correctness of the configured cloud infrastructure. Once registration is complete, an asynchronous process begins to retrieve images and node candidates, and provided authentication can be validated if it is correctly configured (see how isAnyAsyncNodeCandidatesProcessesInProgress and GetCloudImages endpoints can be used for validation). Note that SSH credentials are only utilized during [Cluster Deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#5-cluster-deployment).

Finnaly, keep in mind that the cloud should be properly deregistered using [Remove Clouds](https://github.com/eu-nebulous/sal#91-removeclouds-endpoint) endpoint, so that the used nodes are undeployed from cloud provider, and there are no 'hanging' clouds left inside of the ProActive server which can cause unexpected behaviour especially in a case the same cloud name is used.

#####  2.2. [isAnyAsyncNodeCandidatesProcessesInProgress endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md) - Checking for ongoing asynchronous processes for retrieving cloud images or node candidates.
You should wait until this process returns `false`, indicating that the retrieval of cloud images and node candidates from the cloud provider is complete.

#####  2.3. [GetCloudImages endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md#24--getcloudimages-endpoint) - Retrieving cloud images. 
This endpoint can be used to verify that the cloud images and authentication settings are correct. If there is a problem with authentication, the endpoint will return an error. For issues related to incorrect credentials or insufficient permissions, consult the Execution Adapter [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive). If an image retrieval problem occurs, the image will not be returned by this endpoint.

### 3. Edge device registration

#####  3.1. [RegisterNewEdgeNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/4-edge-endpoints.md#41--registernewedgenode-endpoint) - Registering a New Edge Device.
This endpoint is used to register a new edge device. Upon successful registration, it returns the defined edge node structure, the unique edge device ID, and the node candidate ID representing this device.

Note that during this process, the device is only registered with its associated information, while validation occurs during the actual  [Cluster Deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#5-cluster-deployment), which uses the registered edge node. To fully deregister an edge device, you must use the [Edge Deregistration](https://github.com/eu-nebulous/sal/blob/main/README.md#8-edge-device-deregistration) endpoint, which ensures proper removal from the system.

#####  3.2. [GetEdgeNodes endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/4-edge-endpoints.md#42--getedgenodes-endpoint) - Retrieving All Registered Edge Devices. 
This endpoint retrieves all registered edge devices, providing all information initially returned during the device registration process.

### 4. Filtering of node candidates

#####  4.1. [findNodeCandidates endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/7-node-endpoints.md#71--findnodecandidates-endpoint) -  Filtering Node Candidates Based on Deployment Requirements.
This endpoint allows you to filter node candidates using various criteria to select suitable nodes for deployment. Specify the required conditions for master or worker nodes within the cluster and store the retrieved node candidate IDs for future use.

In NebulOuS, there are only two node types:`IAAS` for the cloud nodes, and `EDGE` for nodes representing edge devices. 

Example of Searching for Node Candidates in an OpenStack Cloud:
- Node Type: IAAS (cloud node)
- Cloud ID: Matches a specific cloud (use {{cloud_name}} to reference)
- Operating System: Ubuntu, version 22
- Region: bgo
- Hardware Specifications: 8GB RAM and 4 CPU cores
  
```json
[
    {
        "type": "NodeTypeRequirement",
        "nodeTypes": ["IAAS"]
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "cloud",
        "requirementAttribute": "id",
        "requirementOperator": "EQ",
        "value": "{{cloud_name}}"
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "image",
        "requirementAttribute": "operatingSystem.family",
        "requirementOperator": "IN",
        "value": "UBUNTU"
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "image",
        "requirementAttribute": "name",
        "requirementOperator": "INC",
        "value": "22"
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "location",
        "requirementAttribute": "name",
        "requirementOperator": "EQ",
        "value": "bgo"
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "hardware",
        "requirementAttribute": "ram",
        "requirementOperator": "EQ",
        "value": "8192"
    },
    {
        "type": "AttributeRequirement",
        "requirementClass": "hardware",
        "requirementAttribute": "cores",
        "requirementOperator": "EQ",
        "value": "4"
    }
]
```

Example of Searching for a Node Candidate Representing an `EDGE` Device:
```json
[
    {
        "type": "NodeTypeRequirement",
        "nodeTypes": ["EDGE"]
    }
]
```
Note that for the `EDGE` devices, their node candidate ID is returned during registration. In a case you target a specific edge device it is to store it during the registration process, or to introduce the unique identifiyer into device name which can be search then using attribute requirement `name` in `hardware` class. 

#####  4.2. [getLengthOfNodeCandidates endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/7-node-endpoints.md#72--getlengthofnodecandidates-endpoint) -  Returns total number of existing node candidates.

### 5. Cluster deployment

#####  5.1. [DefineCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#101--definecluster-endpoint) - Defining Kubernetes cluster.
This endpoint is used to define and configure Kubernetes cluster deployments. When setting up a Kubernetes cluster using this endpoint, [scripts](https://github.com/eu-nebulous/sal-scripts) maintained by NebulOuS developers streamline the deployment process by installing essential software components within the cluster. These scripts and other parts of the deployment workflow can be debugged and tested using [ProActive workflows](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/script-changing-and-testing-using-proactive-workflows), enabling seamless integration and troubleshooting.

The [script templates](https://github.com/ow2-proactive/scheduling-abstraction-layer/tree/master/docker/scripts) provided by SAL offer predefined structures for deployment, allowing for efficient configuration. Ensure that any required [environmental variables](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/env-variables-for-nebulous-application-deployment-scripts)  and their values are specified in the cluster definition; these variables are maintained by the owner of the component that uses them for NebulOuS development purposes.

#####  5.2. [DeployCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#102--deploycluster-endpoint) - Deploying a Kubernetes Cluster.
This endpoint initializes the cluster deployment process. Once started, you can monitor the progress of the deployment.

If the deployment fails (i.e., the SAL does not return `true`), consult the SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (especially ones inside of container) and ProActive [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive) i.e. `connector-iaas.log`.

Note that deployment failures can occur due to various factors. To ensure a successful deployment and execution, confirm that selected cloud and edge nodes are available. Additionally, the information regarding SSH credentials and Execution Adapter scripts used for edge devices during [Cloud Registration](https://github.com/eu-nebulous/sal/blob/main/README.md#2-cloud-registration) or [Edge Device Registration](https://github.com/eu-nebulous/sal/blob/main/README.md#3-edge-device-registration) is validated only at the time of deployment execution.

If the deployment succeeds and returns `true`, you can track the ongoing progress and troubleshoot any issues using the Execution Adapter interface. Monitoring tools include:
- The ProActive dashboard for an overview of the entire deployment,
- The ProActive Scheduler for details on individual task execution, 
- The ProActive Resource Manager to monitor resource utilization.

#####  5.3. [GetCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#103--getcluster-endpoint) - Retrieving Cluster Deployment Status. 
This endpoint provides detailed information on the current status of the Kubernetes cluster deployment.

#####  5.4. [DeleteCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#105--deletecluster-endpoint) - Deleting a Cluster and Undeploying Resources.
This endpoint enables the deletion of an existing Kubernetes cluster deployment. It removes all associated resources, including worker nodes and applications, effectively undeploying the cluster. Use this endpoint to fully dismantle a cluster and free up resources once the deployment is no longer needed.

### 6. Application management

#####  6.1. [ManageApplication endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#104--manageapplication-endpoint) - Managing application deployment.
This endpoint is used to deploy and manage applications within a specified Kubernetes cluster. It supports both the initial deployment of applications and the reconfiguration of application replicas, allowing you to adjust the number of replicas as needed for scaling and performance optimization.

### 7. Cluster reconfiguration

#####  7.1. [ScaleOut endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#106--scaleout-endpoint) - Scaling out the Cluster.
This endpoint enables dynamic expansion of the Kubernetes cluster by adding additional worker nodes as needed. Use this endpoint to increase the cluster's processing capacity and accommodate higher workloads by scaling out with new resources.

#####  7.2. [ScaleIn endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#107--scalein-endpoint) - Scaling In the Cluster
This endpoint allows you to scale in the Kubernetes cluster by removing specified worker nodes. Use this endpoint to decrease the cluster's size, optimize resource usage, and reduce operational costs by deallocating unneeded nodes.

#####  7.3. [LabelNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#108--labelnode-endpoint) - Managing Node Labels
This endpoint allows you to manage node labels within a Kubernetes cluster, enabling you to add, modify, or remove labels on specific nodes. Use this feature to organize and categorize nodes effectively, which can aid in scheduling, resource management, and targeting specific nodes for workloads.

#### Scaling Out the application
To scale out an application, follow these steps:

- _Add New Worker Nodes_: First, use the [ScaleOut endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#106--scaleout-endpoint) to add additional worker nodes to the existing Kubernetes cluster.

- _Label the New Worker Nodes_: Once the new worker nodes are successfully deployed within the cluster, apply appropriate labels to them using the [LabelNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#108--labelnode-endpoint). Proper labeling is essential for organizing and targeting nodes for specific workloads.

- _Increase Application Replicas_: Finally, to complete the scale-out process, adjust the number of application replicas by calling the [ManageApplication endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#104--manageapplication-endpoint). This will ensure the application takes advantage of the newly added worker nodes.

#### Scaling In the application
To scale in an application, follow these steps:

- _Label the Nodes for Removal_: First, use the [LabelNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#108--labelnode-endpoint) to mark specific worker nodes as unavailable for new application replicas. This ensures that no new replicas are assigned to these nodes during the scaling process.

- _Adjust Application Replicas_: Next, call the [ManageApplication endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#104--manageapplication-endpoint) with a reduced number of replicas to gradually remove the application from the marked nodes.

- _Remove Worker Nodes_: Finally, once the application replicas have been removed from the designated nodes, use the [ScaleIn endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#107--scalein-endpoint) to remove the worker nodes from the cluster, optimizing resource usage and reducing operational costs.

### 8. Edge device deregistration

#####  8.1. [DeleteEdgeNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/4-edge-endpoints.md#44--deleteedgenode-endpoint)
This endpoint is used to deregister edge device using it's ID which is returned during (registration process)[https://github.com/eu-nebulous/sal/#3-edge-device-registration] and can be retrived by using GetEdgeNodes endpoint. 

### 9. Cloud deregistration

#####  9.1. [RemoveClouds endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md#27--removeclouds-endpoint)
This endpoint allows you to deregister one or more cloud infrastructures and undeploy its nodes from the cloud provider. 

### 10. SAL Persistance (for developers and project mentors)
SAL supports the clean operations for clusters, clouds, edge devices and the SAL database. These are to be used for maintainance puposes and by NebulOuS developers to assure that all the resources were undeployed and removed properly, not just from SAL, but as well from the ProActive server and cloud provieders. 
#####  10.1. [CleanAll endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/0-persistence-endpoints.md#01---clean-all-endpoint) 
#####  10.1. [CleanAll Clusters endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/0-persistence-endpoints.md#02---clean-all-clusters-endpoint) 
#####  10.1. [CleanAll Clouds endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/0-persistence-endpoints.md#03---clean-all-clouds-endpoint) 
#####  10.1. [CleanAll Edges endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/0-persistence-endpoints.md#04---clean-all-edge-devices-endpoint) 
#####  10.1. [Clean SAL Database endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/0-persistence-endpoints.md#05---clean-sal-database-endpoint) 



# NebulOuS SAL Deployment (managed by 7Bulls)
NebulOuS SAL is deployed with a chart managed at https://github.com/eu-nebulous/helm-charts/tree/main/charts/nebulous-sal
NebulOuS SAL original deployment script can be found at https://github.com/ow2-proactive/scheduling-abstraction-layer/tree/master/deployment

Please bare in mind that the values in the helm chart can be overwritten in the nrec deployment definition:

cd environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-cd/helm-releases/specific-patches/nebulous-sal.yaml

prod environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-prod/helm-releases/specific-patches/nebulous-sal.yaml

test environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-test/helm-releases/specific-patches/nebulous-sal.yaml

dev environment: https://github.com/eu-NebulOuS/nrec-flux-config/blob/main/clusters/primary/NebulOuS-dev/helm-releases/specific-patches/NebulOuS-sal.yaml

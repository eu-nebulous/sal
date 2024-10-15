# Introduction
Deployment Manager, i.e. Scheduling Abstraction Layer (SAL) is an abstraction layer initially developed as part of the EU project Morphemic by [Activeeon](https://www.activeeon.com/). Its development continued through the Nubulous EU project. SAL aims to enhance the usability of Execution adapter, i.e. [ProActive Scheduler & Resource Manager](https://doc.activeeon.com/latest/), by providing abstraction, making it easier for users to interact with the scheduler and take advantage of its features. Seamlessly supporting REST calls and direct communication with the Execution Adapter SAL empowers users to harness the scheduler's capabilities. 

SAL Code repository and documentation can be found [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md).

# Report Issue
In a case of issue, please create the bug error report [here](https://github.com/eu-nebulous/sal/issues). 
When reporting issue, for faster resolution of your problem, please include:
- the description of the scenario e.g. Nebulous sequence diagrams which were executed
- date and time, SAL and ProActive environment
- SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (especially ones inside of container)
- ProActive [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive) i.e. `connector-iaas.log`
- ProActive job id (in a case of error during the ProActive workflow execution)
- detailed description of the action during which it happened 

# NebulOuS Development
Note that there is additional documentation for Nebulous development is provided [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/deployment-manager-sal-1).
For preset Nebulous environment for testing and development, you can find more information on how to access SAL [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/sal-in-nebulous-k8s), and regarding ProActive [here](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/proactive-in-nebulous-k8s) 

# Deployment Manager & Execution Adapter NebulOus scenario
This section describes the sequence of the SAL endpoints provided to support NebulOuS deployment and execution scenario.
It is possible to find more regarding how to use SAL endpoints [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

### 1. Prerequisites
To use SAL, you must have the Execution Adapter (ProActive) [installed](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#proactive-scheduler) and [properly configured](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#configure-proactive-scheduler-details).
In the [configuration script](https://github.com/eu-nebulous/sal/blob/main/resources/deployment.yaml), it is necessary only to set 
- `<PROACTIVE_URL>`
- `<USERNAME>`
- `<PASSWORD>`

The rest of the configuration is automatically handled by Nebulous (see [Nebulous SAL deployment](https://github.com/eu-nebulous/sal/blob/main/README.md#nebulous-sal-deployment-managed-by-7bulls) for more details).

For additional information on setting up the SAL Kubernetes deployment script, refer to [this guide](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#23-deploying-sal-as-a-kubernetes-pod). You can find details on using the endpoints [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

#####  1.1. [Connect endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/1-connection-endpoints.md#11--connect-endpoint) - Establishing the connection to ProActive server. 
SAL must be connected to ProActive to use any of the endpoints. If you encounter an `HTTP 500` when calling endpoints, which reports a `NotConnectedException`, it indicates that SAL is not connected to ProActive. You can verify this in the SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (particularly those within the container).
Keep in mind that the connection to ProActive may be lost during scenario execution and may need to be reestablished.

### 2. Cloud registration
#####  2.1. [Add cloud endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md) - Defining a cloud infrastructure.
To use this endpoint, you must specify a unique `cloud_name` hat has not already been registered. Note that, after a SAL restart, cloud information is erased from the SAL database but not from the Execution Adapter. If you use a name that has already been registered, the infrastructure will not be updated with new information. For more information on setting up cloud providers for Nebulous, see [here](https://github.com/eu-nebulous/nebulous/wiki/2.1-Managing-cloud-providers).

Additionally, while the infrastructure may be registered, this does not guarantee the correctness of the configured cloud infrastructure. Only after registration will an asynchronous process to retrieve images and node candidates begin, provided authentication is used (refer to 2.2. and 2.3. for validation). Furthermore, the SSH credentials are only used during cluster deployment (see 5.2).

#####  2.2. [isAnyAsyncNodeCandidatesProcessesInProgress endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md) - Checking for ongoing asynchronous processes for retrieving cloud images or node candidates.
You should wait until this process returns `false`, indicating that the retrieval of cloud images and node candidates from the cloud provider is complete.

#####  2.3. [GetCloudImages endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/2-cloud-endpoints.md#24--getcloudimages-endpoint) - Retrieving cloud images. 
This endpoint can be used to verify that the cloud images and authentication settings are correct. If there is a problem with authentication, the endpoint will return an error. For issues related to incorrect credentials or insufficient permissions, consult the Execution Adapter [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive). If an image retrieval problem occurs, the image will not be returned by this endpoint.

### 3. Edge device registration

#####  3.1. [RegisterNewEdgeNode endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/4-edge-endpoints.md#41--registernewedgenode-endpoint) - Registering a New Edge Device.
This endpoint is used to register a new edge device. It returns the defined edge node structure, the registered edge device ID, and the node candidate ID representing this device. 

#####  3.2. [GetEdgeNodes endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/4-edge-endpoints.md#42--getedgenodes-endpoint) - Retrieving All Registered Edge Devices. 
This endpoint retrieves all registered edge devices, providing all information initially returned during the device registration process.

### 4. Filtering of node candidates

#####  4.1. [findNodeCandidates endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/7-node-endpoints.md#71--findnodecandidates-endpoint) -  Filtering Node Candidates Based on Deployment Requirements.
This endpoint allows you to filter node candidates using various criteria to select suitable nodes for deployment. Specify the required conditions for master or worker nodes within the cluster and store the retrieved node candidate IDs for future use.

In Nebulous, there are only two node types:`IAAS` for the cloud nodes, and `EDGE` for nodes representing edge devices. 

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

### 5. Cluster deployment

#####  5.1. [DefineCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#101--definecluster-endpoint) - Defining Kubernetes cluster.
This endpoint is used to define and configure Kubernetes cluster deployments. When setting up a Kubernetes cluster using this endpoint, [scripts](https://github.com/eu-nebulous/sal-scripts) maintained by Nebulous developers are used, which streamline the deployment process by installing essential software components within the cluster. The script templates provided by SAL can be found [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/tree/master/docker/scripts). Required [environmental variables](https://openproject.nebulouscloud.eu/projects/nebulous-collaboration-hub/wiki/env-variables-for-nebulous-application-deployment-scripts) and their corresponding values should be included in the cluster definition and are maintained for Nebulous development by the owner of component which is using the respective environmental variable. 

Ensure that cloud or edge nodes are both added and selected to enable successful deployment and execution.

#####  5.2. [DeployCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#102--deploycluster-endpoint) - Deploying a Kubernetes Cluster.
This endpoint initializes the cluster deployment process. Once started, you can monitor the progress of the deployment.

If the deployment fails (i.e., the SAL does not return `true`), consult the SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (especially ones inside of container) and ProActive [logs](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#how-to-check-the-logs-of-proactive) i.e. `connector-iaas.log`.

If the deployment succeeds and returns `true`, you can track the ongoing progress and troubleshoot any issues using the Execution Adapter interface. Monitoring tools include:
- The ProActive dashboard for an overview of the entire deployment,
- The ProActive Scheduler for details on individual task execution, and
- The ProActive Resource Manager to monitor resource utilization.

#####  5.3. [GetCluster endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/10-cluster-endpoints.md#103--getcluster-endpoint) - Retrieving Cluster Deployment Status. 
This endpoint provides detailed information on the current status of the Kubernetes cluster deployment.

### 6. Initial application deployment

#####  6.1. []()

### 7 Reconfiguration

#####  7.1. []()

#####  7.2. []()

#####  7.3. []()

#### 7.1. Scaling Out the application

#### 7.2. Scaling In the application

### 8. Edge device deregistration (TBD)

#####  8.1. []()

### 9. Cloud deregistration (TBD)

#####  9.1. []()


# NebulOuS SAL Deployment (managed by 7Bulls)
NebulOuS SAL is deployed with a chart managed at https://github.com/eu-nebulous/helm-charts/tree/main/charts/nebulous-sal
NebulOuS SAL original deployment script can be found at https://github.com/ow2-proactive/scheduling-abstraction-layer/tree/master/deployment

Please bare in mind that the values in the helm chart can be overwritten in the nrec deployment definition:

cd environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-cd/helm-releases/specific-patches/nebulous-sal.yaml

prod environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-prod/helm-releases/specific-patches/nebulous-sal.yaml

test environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-test/helm-releases/specific-patches/nebulous-sal.yaml

dev environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-dev/helm-releases/specific-patches/nebulous-sal.yaml

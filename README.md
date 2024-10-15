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

# NebulOuS SAL Deployment (managed by 7Bulls)
NebulOuS SAL is deployed with a chart managed at https://github.com/eu-nebulous/helm-charts/tree/main/charts/nebulous-sal
NebulOuS SAL original deployment script can be found at https://github.com/ow2-proactive/scheduling-abstraction-layer/tree/master/deployment

Please bare in mind that the values in the helm chart can be overwritten in the nrec deployment definition:

cd environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-cd/helm-releases/specific-patches/nebulous-sal.yaml

prod environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-prod/helm-releases/specific-patches/nebulous-sal.yaml

test environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-test/helm-releases/specific-patches/nebulous-sal.yaml

dev environment: https://github.com/eu-nebulous/nrec-flux-config/blob/main/clusters/primary/nebulous-dev/helm-releases/specific-patches/nebulous-sal.yaml

# Deployment Manager & Execution Adapter NebulOus scenario
This section describes the sequence of the SAL endpoints provided to support NebulOuS deployment and execution scenario.
It is possible to find more regarding how to use SAL endpoints [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

### 1. Prerequisites
To use SAL, it is mandatory to have the Execution Adapter (ProActive) [installed](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#proactive-scheduler) and [properly configured](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#configure-proactive-scheduler-details).
In the [configuration script](https://github.com/eu-nebulous/sal/blob/main/resources/deployment.yaml), it is necessary only to set 
- `<PROACTIVE_URL>`
- `<USERNAME>`
- `<PASSWORD>`

Rest is configured automatically for Nebulous (see [Nebulous SAL deployment]().
For more information on setting up the SAL Kubernetes deployment script, see [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#23-deploying-sal-as-a-kubernetes-pod). Details about using the endpoints are available [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#31-using-sal-rest-endpoints).

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
This endpoint can be used to verify that the cloud images and authentication settings are correct. If there is a problem with authentication, the endpoint will return an error. For issues related to incorrect credentials or insufficient permissions, consult the Execution Adapter logs. If an image retrieval problem occurs, the image will not be returned by this endpoint.

### 3. Edge device registration

### 4. Filtering of node candidates

### Cluster deployment

### Initial application deployment

### Reconfiguration

#### Scaling Out the application

#### Scaling In the application

### Edge device deregistration (TBD)

### Cloud deregistration (TBD)


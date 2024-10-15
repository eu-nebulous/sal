# Introduction
Deployment Manager, i.e. Scheduling Abstraction Layer (SAL) is an abstraction layer initially developed as part of the EU project Morphemic. Its development continued through the Nubulous EU project. SAL aims to enhance the usability of ProActive Scheduler & Resource Manager by providing abstraction, making it easier for users to interact with the scheduler and take advantage of its features. Seamlessly supporting REST calls and direct communication with the Execution Adapter, i.e. Proactive API, SAL empowers users to harness the scheduler's capabilities. 

SAL Code repository and documentation can be found [here](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md).
To use SAL it is manadtory to have the Execution Adapter (ProActive) installed: [here](https://github.com/eu-nebulous/nebulous/wiki/1.1-Installation-Walk%E2%80%90trough-for-Development-&-Evaluation#proactive-scheduler). 

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

### Prerequisites
SAL need to be connected to the ProActive. If you are getting the `HTTP 500` error when calling the endpoints, which state NotConnectedException, it means that SAL is not connected to ProActive. 
This also can be seen in SAL [logs](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/README.md#32-view-sal-logs) (especially ones inside of container).

- [Connect endpoint](https://github.com/ow2-proactive/scheduling-abstraction-layer/blob/master/endpoints/1-connection-endpoints.md#11--connect-endpoint) - Establishing the connection to ProActive server. 

### Cloud registration


### Edge device registration

### Filtering of node candidates

### Cluster deployment

### Initial application deployment

### Reconfiguration

#### Scaling Out the application

#### Scaling In the application

### Edge device deregistration (TBD)

### Cloud deregistration (TBD)


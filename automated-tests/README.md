# SAL Automated Tests

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
  - [Install Node.js and npm](#1-install-nodejs-and-npm)
  - [Install Newman](#2-install-newman)
  - [Verify Newman Installation](#3-verify-newman-installation)
- [Single Cloud Automated Test](#single-cloud-automated-test)
  - [Test Workflow](#test-workflow)
  - [Editing and Running the Test](#editing-and-running-the-test)
  - [Running the Test](#running-the-test)

## Introduction
SAL automated tests are based on Postman collection and environment files. Please note that the difference between a pure Postman collection to make the calls and one meant for automated testing is that the latter contains pre- and post-scripts to validate the results and direct the flow of the test.

Newman is a command-line tool that allows you to run Postman collections. This guide will walk you through the steps to install and use Newman for running tests.

## Prerequisites
Before installing Newman, ensure the following prerequisites are met:

- **Postman**: You have Postman installed.
- **Node.js**: A runtime environment for executing JavaScript.
- **npm** (Node Package Manager): A tool for managing JavaScript packages.

To install Node.js and npm, visit the official Node.js website: [https://nodejs.org/](https://nodejs.org/)

## Installation Steps

### 1. Install Node.js and npm
Download and install Node.js from the official website. The installation of Node.js will also install npm automatically.

After installation, verify that Node.js and npm have been installed correctly by running the following commands in your terminal or command prompt:

```sh
node -v
npm -v
```

These commands will return the installed versions of Node.js and npm.

### 2. Install Newman
Once Node.js and npm are installed, you can install Newman globally using npm. Run the following command:

```sh
npm install -g newman
```

This will install Newman globally, making it accessible from anywhere on your system.

### 3. Verify Newman Installation
To verify that Newman has been successfully installed, run the following command:

```sh
newman -v
```

This will display the installed version of Newman.

Note that in some cases, it may be necessary to adjust the system's path variables for npm and Newman to work locally.

## Single Cloud Automated Test
Currently, we have an automated test for a single cloud NebulOuS deployment and reconfiguration.

### Test Workflow
1. Adding cloud resources
2. Selecting node candidates for deployment
3. Deploying the cluster and application with 1 replica
4. Scaling out the cluster and deploying the application with 2 replicas
5. Scaling in the cluster and deploying the application with 1 replica
6. Scaling in the cluster and removing the application
7. Deleting the cluster
8. Deregistering the cloud provider

### Editing and Running the Test

To run tests, it is necessary to edit the provided `.json` templates in Postman or as raw JSON files.

#### Editing the Postman Collection
First, edit `SALSingleCloudTest.json`, which is a Postman collection containing API endpoints. The key difference between this collection and the one intended for manual execution is that it contains pre- and post-scripts that validate received results and determine the next step in the automated workflow.

The following calls need to be updated:
- Add the `AddCloud` call (currently, it only contains the cloud name bound to an environmental variable).
- Add any additional criteria for `FindNodeCandidates_master` and `FindNodeCandidates_worker`.
- In `DefineCluster`, following environment variables are preset for the `nebulous-cd` environment. Ensure that these environment variables are updated to support running tests in targeted test environment.
```json
{
  "APPLICATION_ID": "AEtest2024090511testosedge2",
  "BROKER_ADDRESS": "158.37.63.86",
  "ACTIVEMQ_HOST": "158.37.63.86",
  "BROKER_PORT": "32754",
  "ACTIVEMQ_PORT": "32754",
  "ONM_IP": "158.39.201.249",
  "ONM_URL": "https://onm.cd.nebulouscloud.eu",
  "AMPL_LICENSE": "dontlookatthis"
}
```



#### Editing the Postman Environment
Next, edit `SALNebulOuSCDenvironment.json`, which is a preset Postman environment. It is recommended to rename all existing named variables to avoid conflicts if multiple users run tests using the same environment. Additionally, this environment file contains credentials for the Nebulous CD environment. If you intend to run the test in another environment, update the username and password accordingly.

#### Running the Test
Finally, establish a connection to SAL (e.g., by setting up a port-forward), then, in another terminal window, run the test using the following command:

```sh
newman run SALSingleCloudTest.json -e SALNebulOuSCDenvironment.json
```

You are free to change the name of the Postman collection and environment file. If you do, ensure that you update the Newman command accordingly to reflect these changes.

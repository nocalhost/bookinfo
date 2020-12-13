# Develop bookinfo with nocalhost

This is a demo project for nocalhost. Bookinfo is from Istio samples(https://github.com/istio/istio/tree/master/samples/bookinfo). 

# Micro Services
Bookinfo is a typical microservice architecture application, which consists of 4 services:

- productpage(Request Entrance): https://github.com/nocalhost/bookinfo-productpage
- reviews: https://github.com/nocalhost/bookinfo-reviews
- details: https://github.com/nocalhost/bookinfo-details
- ratings: https://github.com/nocalhost/bookinfo-ratings

Every service has its different program language and runtime environment. All of them had been configured to use docker as container runtime, you can find Dockerfile in their corresponding repository.

# Helm 
A helm chart in this repository is prepared for one command installing. 4 Deployments witch 2 replica pods for 4 microservices are main skeleton of this chart.

You can install the chart by this command: `helm install ./charts/bookinfo`, but if you want to try nocalhost, we recommend you do this with nocalhost. We will give step-by-step QuickStart later.

# What we changed compared to istio bookinfo?

We commit some changes to demonstrate nocalhost better. Here are some main changes:

- Simplified different version of reviews service to one version. Nocalhost does not target on how to manage service traffic or canary deployment.
- Splitted source codes from mono-repo to four independent repositories. In reality, diffrent microservices are developed by diffrent teams with different access rules.
- Changed the framework of reviews service to spring-boot. Nothing but everyone loves spring-boot.
- Configured GitHub Action for every microservice to auto build Docker images.
- Added a .nocalhost directory to support development with nocalhost.

# About `.nocalhost` directory

`.nocalhost` is a directory in the root of repository which stores the configurations of nocalhost. In this case, the file `config.yaml` :

```yaml
name: bookinfo
manifestType: rawManifest
resourcePath: ["manifest/templates"]

onPreInstall:
  - path: manifest/templates/pre-install/print-num-job-01.yaml
    weight: "1"
  - path: manifest/templates/pre-install/print-num-job-02.yaml
    weight: "-5"

services:
  - name: productpage
    serviceType: deployment
    gitUrl: https://github.com/nocalhost/bookinfo-productpage
    devContainerImage: codingcorp-docker.pkg.coding.net/nocalhost/dev-images/python:3.7.7-slim
    workDir: /home/nocalhost-dev
    syncDirs:
    - ./
    devPorts:
    - :9080
    dependJobsLabelSelector:
    - "dep-job"
  - name: details
    serviceType: deployment
    gitUrl: https://github.com/nocalhost/bookinfo-details.git
    devContainerImage: codingcorp-docker.pkg.coding.net/nocalhost/dev-images/ruby:2.7.1-slim
    syncDirs:
    - ./
    workDir: /home/nocalhost-dev
  - name: ratings
    serviceType: deployment
    gitUrl: https://github.com/nocalhost/bookinfo-ratings.git
    devContainerImage: codingcorp-docker.pkg.coding.net/nocalhost/dev-images/node:12.18.1-slim
    workDir: /home/nocalhost-dev
    syncDirs:
    - ./
    dependJobsLabelSelector:
    - "dep-job"
    dependPodsLabelSelector:
    - "productpage"
    - "app.kubernetes.io/name=productpage"
  - name: reviews
    serviceType: deployment
    gitUrl: https://github.com/nocalhost/bookinfo-reviews.git
    devContainerImage: codingcorp-docker.pkg.coding.net/nocalhost/dev-images/java:latest
    workDir: /home/nocalhost-dev
    syncDirs:
    - ./
    dependPodsLabelSelector:
    - "productpage"
```

# How to start?

https://nocalhost.dev/quickstart

# Contribute
https://nocalhost.dev/contribute


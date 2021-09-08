# Develop bookinfo with nocalhost

This is a nocalhost config demo project. Bookinfo is from Istio samples(https://github.com/istio/istio/tree/master/samples/bookinfo). 

# Install
Use `helm install whatever ./chart/bookinfo` to use the default `values` to install bookinfo,
this installation will use this `chart/bookinfo/example/config-from-cm/nocalhost-service-config.yaml`
cm configuration file to install, experience pure cm nocalhost installation.

Using the `values` `values-full-config.yaml` to install bookinfo, is also a pure cm experience,
but the app-level config is indicated, which is slightly different.

Finally, use `values-annotation-config.yaml` to install, it will use cm as values 
and also fill in the value of annotations, experience annotations & cm to install the application


# Micro Services
Bookinfo is a typical microservice architecture application, which consists of 5 services:

- productpage(Request Entrance): https://github.com/nocalhost/bookinfo-productpage
- reviews: https://github.com/nocalhost/bookinfo-reviews
- details: https://github.com/nocalhost/bookinfo-details
- ratings: https://github.com/nocalhost/bookinfo-ratings
- authors: https://github.com/nocalhost/bookinfo-authors

Every service has its different program language and runtime environment. All of them had been configured to use docker as container runtime, you can find Dockerfile in their corresponding repository.

# Helm 
A helm chart in this repository is prepared for one command installing. 5 Deployments witch 2 replica pods for 5 microservices are main skeleton of this chart.

You can install the chart by this command: `helm install ./charts/bookinfo`, but if you want to try nocalhost, we recommend you do this with nocalhost. We will give step-by-step QuickStart later.

# What we changed compared to istio bookinfo?

We commit some changes to demonstrate nocalhost better. Here are some main changes:

- Simplified different version of reviews service to one version. Nocalhost does not target on how to manage service traffic or canary deployment.
- Splitted source codes from mono-repo to five independent repositories. In reality, diffrent microservices are developed by diffrent teams with different access rules.
- Changed the framework of reviews service to spring-boot. Nothing but everyone loves spring-boot.
- Configured GitHub Action for every microservice to auto build Docker images.
- Added a .nocalhost directory to support development with nocalhost.

# How to start?

https://nocalhost.dev/docs/quick-start/

# Contribute

https://github.com/nocalhost/nocalhost/blob/main/CONTRIBUTING.md


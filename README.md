# Develop bookinfo with nocalhost

This is a demo project for nocalhost. Bookinfo is from Istio samples(https://github.com/istio/istio/tree/master/samples/bookinfo). 

# Micro Services
Bookinfo is a typical microservice architecture, which consists of 4 services:

- productpage(Request Entrance): https://github.com/nocalhost/bookinfo-productpage
- reviews: https://github.com/nocalhost/bookinfo-reviews
- details: https://github.com/nocalhost/bookinfo-details
- ratings: https://github.com/nocalhost/bookinfo-ratings

Every service has its different program language and runtime environment. All of them had been configured to use docker as container runtime, you can find Dockerfile in their corresponding repository.

# helm 
A helm chart is prepared for one command installing. 4 Deployments witch 2 replica pods for 4 microservices are is main skeleton of this chart.

You can install the chart by this command: `helm install ./charts/bookinfo`, but if you wang try nocalhost, we recommend you do this with nocalhost. We will give step-by-step tutorial later.

# What we change from istio bookinfo?

We commit some changes to demonstrate nocalhost better. Here is the items:

- Simplified different version of reviews service to one version. Nocalhost does not target on how to manage service traffic or canary deployment.
- Splitted source codes from mono-repo to four independent repositories. In reality, diffrent microservices are developed by diffrent teams with different access roles.
- Changed the framework of reviews service to spring-boot. Nothing but everyone love spring-boot.
- Configured GitHub Action for every microservice to auto build Docker images.

# How to start?

(https://nocalhost.dev/quickstart)[https://nocalhost.dev/quickstart]

# Contribute
(https://nocalhost.dev/contribute)[https://nocalhost.dev/contribute]


# Develop bookinfo with nocalhost

This is a demo project for nocalhost. Bookinfo is from Istio samples(https://github.com/istio/istio/tree/master/samples/bookinfo). 

# Micro Services
Bookinfo is a typical microservice architecture, which consists of 4 services:

- productpage(Request Entrance): https://github.com/nocalhost/bookinfo-productpage
- reviews: https://github.com/nocalhost/bookinfo-reviews
- details: https://github.com/nocalhost/bookinfo-details
- ratings: https://github.com/nocalhost/bookinfo-ratings

Every service has its different program language and runtime environment. All of them had been configured to use docker as container runtime, you can find Dockerfile in their corresponding repository.

# Helm 
A helm chart in this repositoty is prepared for one command installing. 4 Deployments witch 2 replica pods for 4 microservices are is main skeleton of this chart.

You can install the chart by this command: `helm install ./charts/bookinfo`, but if you wang try nocalhost, we recommend you do this with nocalhost. We will give step-by-step tutorial later.

# What we changed from istio bookinfo?

We commit some changes to demonstrate nocalhost better. Here is the items:

- Simplified different version of reviews service to one version. Nocalhost does not target on how to manage service traffic or canary deployment.
- Splitted source codes from mono-repo to four independent repositories. In reality, diffrent microservices are developed by diffrent teams with different access roles.
- Changed the framework of reviews service to spring-boot. Nothing but everyone love spring-boot.
- Configured GitHub Action for every microservice to auto build Docker images.

# About `.nocalhost` directory

`.nocalhost` is a directory in the root of repository which stores the configurations of nocalhost. In this case, the file `config.yaml` :

```
# preInstalls indict the jobs must be done before application installation.
preInstalls:
  - path: manifest/pre-install/print-num-job-01.yaml
  - path: manifest/pre-install/print-num-job-02.yaml
    # job priority, the smaller the earlier.
    weight: "10"  
  - path: manifest/pre-install/print-num-job-03.yaml
    weight: "-5"

# Application infomation
appConfig:
  name: bookinfo
  type: helm
  resourcePath: charts/bookinfo

# Configurations of microservices in application
svcConfigs:
    # the microservice name(same as the name of Kubernete Object)
  - name: productpage
    # the resouces type in Kubernetes(deployment is the only surppoted type by now)
    type: deployment
    # the git clone url of this microservice(used by IDE plugin)
    gitUrl: https://github.com/nocalhost/bookinfo-productpage
    # TODO
    devEnv: python  # java|go|node|php
    # The container image for development of this microservice(Commonly, the image contains all of the sdks and debug tools of this microservice)
    devImage: codingcorp-docker.pkg.coding.net/nocalhost/public/share-container-ruby:v2  # 可选参数：自定义开发容器
    # Specify which directories to sync from IDE workspace to remote container
    sync: 
    - .:/home  # 本地 . 目录同步到远程 /home 目录（脚本语言推荐），注意需要使用 empty dir 挂载到业务容器和工作容器对应的目录下，实现容器间共享存储。
    # - ./build:/home  # 本地 build 目录同步到远程 home 目录，例如大型 Java 项目不推荐同步源码，应本地 build 之后，同步 jar 包即可
    # Specify which directories to ignore sync
    ignore:
      - tests # 忽略本地 /test 目录不同步
      - .github
    # Specify which ports to forward to local
    devPort:
    - 12345:22  # 打通开发容器的 ssh 端口用于同步文件，该选项可以考虑内置，随机选择本地端口打通开发容器的 ssh，对用户透明。
    - 5005:5005  # 打通应用的调试端口，使用 port-forward 一致的格式 [locaPort:remotePort]
    
    # Specify which command to execute when enabled hotload development
    command: ["kill `ps -ef|grep -i gradlew| grep -v grep| awk '{print $2}'`", "gradlew", "bootrun"]
    
    # Specify which jobs to wait before this microservice starts
    jobs:  # 依赖的 job 定义
    - "dep-job"
    
    # Specify which pods to wait for ready before this microservice starts
    pods:  # 依赖的 pod 定义
    - "productpage"
```

# How to start?

(https://nocalhost.dev/quickstart)[https://nocalhost.dev/quickstart]

# Contribute
(https://nocalhost.dev/contribute)[https://nocalhost.dev/contribute]


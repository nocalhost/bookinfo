# README

#### Annotation 注入依赖

1. helm dep build
2. helm install bookinfo . -f values-annotation-config.yaml --namespace xx --kubeconfig xxx

#### ConfigMap 注入依赖

1. helm dep build
2. helm install bookinfo . -f values-full-config.yaml --namespace xx --kubeconfig xxx

******

## 文档说明

#### 一、如何正确配置 Nocalhost `Cm config` 与 `Annotation Config`

`Cm Config`  与 `Annotation Config` 的配置规则本质上是一致的，例如，可直接将上述配置放置在指定位置。



我们这里将以 https://github.com/nocalhost/bookinfo 的 `config/example` 分支为例，讲解如何配置 `Cm config` 与 `Annotation Config`



##### 1.1 `Cm Config` 的配置

我们看到  `values.yaml`，这里声明了使用 `example/config-from-cm/nocalhost-service-config.yaml` 来注入 `configmap`



首先，`example/config-from-cm/nocalhost-service-config.yaml`  是一个半标准的 `nocalhost config`，它缺失了 app 级的配置，我们同样支持。



当然，也可以使用`example/config-from-cm/nocalhost-full-config.yaml`，这个 yaml 则是一个完全标准的 `nocalhost config`。



`nocalhost config` 的内容本身这里不做赘述，我们来看看`charts/bookinfo/templates/nocalhost-app-config.yaml`

```
apiVersion: v1
kind: ConfigMap
metadata:
  # [Nocalhost]: nocalhost configmap should be named with dev.nocalhost.config.${appName}
  name: "dev.nocalhost.config.{{ .Release.Name }}"
  # [Nocalhost]: labels {dep-management: nocalhost} is also required
  labels:
    dep-management: nocalhost
  annotations:
    "helm.sh/hook": pre-install
data:
  config: |-
    {{ .Files.Get .Values.nocalhost.config.path | nindent 4 }}
```



这个 configmap 有四点值得注意：



1. 命名规则，它的命名规则必须是 dev.nocalhost.config.${appName}，也就是 `name: "dev.nocalhost.config.{{ .Release.Name }}"`

2. 标签，它需要有一个标签键值对，固定写死为 `dep-management: nocalhost`
3. config 以文本块的方式放置在 data.config 下，注意配置需要有四个缩进。
4. 执行时机必须先于其他工作负载，可以看到这里使用了 helm 的注解来进行实现：`"helm.sh/hook": pre-install`



这些配置将在正确部署了 nocalhost-dep 后，在安装应用时生效。

无论是否部署 nocalhost-dep，配置将在插件中点击工作负载右键 `DevConfig` 获取到，实时生效。



##### 1.2 `Annotations Config` 的配置

还是看到 values 配置，这次看到 `values-annotation-config.yaml`，它声明了 localhost-annotations-config 的位置：

```
  annotations:
    path:
      authors: example/config-from-annotations/authors.yaml
      details: example/config-from-annotations/details.yaml
      productpage: example/config-from-annotations/productpage.yaml
      ratings: ""
      reviews: ""
```



在具体的工作负载声明中，将使用这个路径来读取配置：

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: authors
  {{- if .Values.nocalhost.annotations.path.authors }}
  annotations:
    dev.nocalhost: |-
      {{ .Files.Get .Values.nocalhost.annotations.path.authors | nindent 6 }}
  {{- end }}
```



这个 annotations 只需要注意以下这点：

1. key 必须是 `dev.nocalhost:`，值则为 `Nocalhost Config` 的文本块



这些配置将在正确部署了 nocalhost-dep 后，在安装应用时生效。

无论是否部署 nocalhost-dep，配置将在插件中点击工作负载右键 `DevConfig` 获取到，实时生效。



#### 二、优先级

`Annotations Config` 优先级高于 `Cm config`



#### 三、配置的粒度

粒度为服务级，也就是说，我们允许 details 这个 deployment 使用 `Cm config`，且 ratings 这个 deployment 使用 `Annotations config`，同时，其他的工作负载使用任意一种配置。


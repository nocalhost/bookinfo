# README

## Annotation 注入依赖

1. helm dep build
2. helm install bookinfo . -f values-annotation-config.yaml --namespace xx --kubeconfig xxx

## ConfigMap 注入依赖

1. helm dep build
2. helm install bookinfo . -f values-full-config.yaml --namespace xx --kubeconfig xxx
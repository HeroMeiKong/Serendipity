# Kubectl 笔记

1. `kubectl get pods -n [namespace]`：列出空间下的容器
2. `kubectl get pods -n [namespace] [image_name] -o jsonpath='{.metadate.uid}'`: 获取指定空间下某镜像的 id

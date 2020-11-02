示例部署

1. 创建secret对象 里面包括'serving.crt'和'serving.key' 用于服务https流量。[hack/gencerts.sh]可以带来帮助。

2. `kubectl create namespace custom-metrics` 在安装k8s-prometheus-adapter之前确保它已经存在

3. `kubectl create -f manifests/` 请进行必要修改Deployment指向你自己的prometheus-server 和 ConfigMap包括你自己的配置。[如果你是按照本指南安装的话，当然你可以完全不进行修改]

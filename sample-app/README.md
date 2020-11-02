目录文件介绍
==========================

1. sample-app-deployment.yaml 是sample-app部署用的deployment yaml文件
2. sample-app-service.yaml 是sample-app部署用的service yaml文件
3. sample-app-service-monitor.yaml 是获取应用层级metrics的yaml文件【对应prometheus scrape job】
4. sample-app-hpa-custom-metrics.yaml 是依据上一条上一条获取的应用层级metrics 进行自动扩缩容HPA yaml文件 


示例部署
==========================

1. `kubectl create namespace default` 在部署sample-app前，请确保此命名空间存在
2. `kubectl apply -f .` 安装sample-app deployment service custom-metrics-hpa service-monitor

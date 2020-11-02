# 参考https://github.com/DirectXMan12/k8s-prometheus-adapter/blob/master/docs/walkthrough.md

项目简介
==========================

此项目是一个k8s-prometheus-adapter安装、了解、学习toturial。旨在学习和交流
此项目为帮助那些想要了解如何将业务层面的自定义指标提供给k8s集群中扩缩容。
用于针对业务指标级别的自动扩缩容。

前言
---------------------

此项目是个人学习项目，不会是一个大而全的整体解决方案。只是想提供给业务指标扩缩容
的一个入门参考，同时也是对我自己的一个学习旅程的记录。


项目概述
---------------------
此项目由多个模块组成。包括prometheus-operator（致力于打造一个k8s集群内更加易用的prometheus堆栈）
、grafana（数据展示）、sample-app（一个简易的Node.js app）、k8s-prometheus-adapter（k8s和prometheus之间的桥梁）
和metrics-server（k8s native 指标服务器）


metrics-server VS k8s-prometheus-adapter
---------------------

这二者不是替补关系，二者缺一不可。从提供的api路径上分析，metrics-server针对/apis/metrics.k8s.io/v1beta1提供服务；
k8s-prometheus-adapter是服务于/apis/custom.metrics.k8s.io 和 /apis/external.metrics.k8s.io
从功能上看，metrics-server提供对pod、node级别资源查看 `kubectl top nodes; kubectl top pods` 以及HPA基于cpu、mem自动扩缩容
的指标数据。 k8s-prometheus-adapter 提供的业务层面的指标 并且HPA v2beta1版本可以支持基于业务自定义指标的自动扩缩容。


基于业务指标自定义扩缩容实现方式
---------------------

首先 为什么需要投入精力做这件事呢？如果是对k8s有点经验的工程师，都会认识到HPA的重要性以及目前HPA遇到的困境。以目前HPA使用方式
来看，无非是根据pod内的cpu、mem来扩缩容。但，实际上，我们更加需要的是根据业务指标来扩缩容，这样才会更加有意义。

下面我们来谈谈业务指标数据是如何流转的
1. 业务指标数据获取：这里我采用的是社区推荐方案[prometheus]来收集 采集配置也很简单明了 只需要定义servicemonitor对象即可[具体配置可参考 sample-app/sample-app-service-monitor.yaml] Note: 业务指标需要使用prometheus社区定义的方式暴露出来
2. 指标数据存储：和上一条一致。都是采用prometheus存储
3. 数据转换\翻译：使用k8s-prometheus-adapter实现。具体步骤可以参考[https://github.com/DirectXMan12/k8s-prometheus-adapter/blob/master/docs/config-walkthrough.md]
4. HPA使用自定义指标进行扩缩容。[具体定义yaml可以参考 sample-app/sample-app-hpa-custom-metrics.yaml]


示例部署步骤
---------------------

1. `kubectl create -f prometheus-operator`  部署一个prometheus-operator实例 并创建prometheus和alertmanager等资源对象
2. `kubectl create -f metrics-server.yaml` 部署一个metrics-server服务
3. 部署k8s-prometheus-adapter [参考k8s-prometheus-adapter/README.md] 
4. `kubectl create -f sample-app` 部署一个sample-app服务
5. `kubectl create -f grafana` 部署grafana服务


后续
---------------------

在上述示例部署完成后，你就可以使用一些ab等测试工具，对sample-app暴露出来的接口进行测试。可以看到sample-app会随着请求量的增长，副本数也会随之增长。
如果你是一个图表爱好者的话，可以尝试着将指标数据在grafana展示出来。[对了，grafana安装后，用户名和密码默认为admin 一定要记得修改密码喔～]
因为我是在docker-for-desktop Mac版本进行测试的。所以可能会存在一些不兼容的情况。初学者不要着急，可以提交Issue寻求帮助和解答。

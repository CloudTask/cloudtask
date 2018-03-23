# CloudTask

![Logo](https://avatars0.githubusercontent.com/u/28881302?s=150&v=4)
## 什么是Cloudtask?

`Cloudtask` 是一个轻量级的分布式定时任务管理平台，它非常轻量小巧，使用简单，我们可以上传、暂停/启动任务，克隆一个任务，修改任务的执行命令，或者查看任务的调度状态、执行日志，开发人员可以采用自己最熟悉的编程语言来编写任务程序，可以是一个控制台程序，也可以是一段 shell 脚本，上传至平台后设置定时执行规则即可。

![架构图](https://cloudtask.github.io/cloudtask/_media/cloudtask.png)

## 设计方案

`Cloudtask` 后端为无状态服务，通过 `Zookeeper` 实现集群各节点间的发现与协调，节点以心跳方式注册到注册中心，中心服务器统一管理着所有集群，任务调度以集群方式进行分配，具有良好的可扩展性，任务分配信息按集群划分并将分配数据存储到 `Zookeeper` 节点，保持分配数据的一致性。

## 主要特性   

- 分布式：采用 `Zookeeper` 实现注册中心，节点与任务分布；
- 跨平台：支持 `Windows` 和 `Linux` 两个平台的部署；
- 任务定时：严格按照任务调度周期执行，同一时间点同一任务只会调度触发一次执行，保持状态一致性；
- 任务控制：可以随时手动启动、停止某一个任务；
- 并行调度：集群中各个节点并行调度本地各自的任务；
- 弹性扩展：节点以环型结构组成集群，加入退出集群更灵活，部分任务会根据节点重新分配；
- 故障转移：当出现节点故障宕机时，为避免集群任务调整抖动，只对故障节点的任务进行转移；
- 运维管理：通过前端站点集中管理所有任务与集群信息，包括用户权限管理；
- 事件通知：若任务执行失败，或集群节点离线，系统会以事件邮件通知运维者；
- 信息收集：节点会将任务执行结果回馈到中心服务器，日志展示更直观；
- 稳定保障：在服务器无波动的情况下，并不会重新分配任务；

## 组件介绍

* [CloudTask InitConfig](https://github.com/cloudtask/cloudtask-initconfig)
* [CloudTask Web](https://github.com/cloudtask/cloudtask-web)
* [CloudTask Center](https://github.com/cloudtask/cloudtask-center)
* [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)

### 项目介绍 

* 项目地址：[https://github.com/cloudtask](https://github.com/cloudtask)

* 项目文档：[https://cloudtask.github.io/cloudtask](https://cloudtask.github.io/cloudtask)

* 在线示例: [http://104.225.159.143:8091](http://104.225.159.143:8091)   

  登录账户：guest 登录密码：123456
## 功能预览

前端登录

![登录](https://cloudtask.github.io/cloudtask/_media/login.png)

Dashboard

![Dashboard](https://cloudtask.github.io/cloudtask/_media/dashboard.png)

集群状态

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/runtime_states.png)

任务列表

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/tasks_list.png)

## License
Cloudtask 所有开源代码基于 Apache Licence 2.0
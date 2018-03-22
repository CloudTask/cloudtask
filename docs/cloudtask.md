# 一个轻量级分布式任务调度平台CloudTask - Quick Start

## 什么是Cloudtask?

Cloudtask是一个轻量级的分布式定时任务管理平台，它非常轻量小巧，使用简单，我们可以上传、暂停/启动任务，克隆一个任务，修改任务的执行命令，或者查看任务的调度状态和执行日志，开发人员可以采用自己最熟悉的编程语言来编写一个任务程序，任务程序可以是一个控制台程序，也可以是一段 shell 脚本，上传至平台后设置任务的定时执行规则周期即可。

![架构图](https://cloudtask.github.io/cloudtask/_media/cloudtask.png)

## 功能特点

- 易用性：通过Web界面管理，操作简单方便，可快速部署各种任务；
- 跨平台：目前支持 Windows 和 Linux 两个平台；
- 跨语言：任务编写可采用任何开发语言，甚至是 shell 脚本；
- 可靠性：节点故障迁移，只要集群存在有效节点，就不会存在因服务器宕机而任务无法执行的情况；
- 扩展性：可以动态加入或退出集群节点，任务会动态调整分配；
- 一致性：同一时间点同一任务只会调度触发一次执行；

## 功能预览

前端登录

![登录](https://cloudtask.github.io/cloudtask/_media/login.png)

Dashboard

![Dashboard](https://cloudtask.github.io/cloudtask/_media/dashboard.png)

集群状态

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/runtime_states.png)

任务列表

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/tasks_list.png)

Activitys

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/activity.png)

任务查询

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/search.png)

用户管理

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/user_manage.png)

集群管理

![Runtime集群](https://cloudtask.github.io/cloudtask/_media/runtime_manage.png)

### 项目地址

- 文档地址：https://cloudtask.github.io/cloudtask   

- 项目地址：https://github.com/cloudtask

#### 授权协议：Apache 2.0  
#### 开发语言：Golang、TypeScript   
#### 操作系统: 跨平台

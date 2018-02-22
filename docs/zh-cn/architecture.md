### Cloudtask架构

![架构图](_media/cloudtask.png)

#### 系统角色

- [CloudTask Web](https://github.com/cloudtask/cloudtask-web)   
   
   
   Cloudtask平台前端网站系统，通过站点可视化管理任务信息、分组信息、集群信息和系统配置参数；   
   站点提供更直观的展示功能，比如 `Dashboard`、`Activity`、 `Task`、`Search Job`、`Runtime` 等模块。   
   通过 `Task` 任务列表可以按分组方式快速查看任务状态和执行的日志信息。

- [CloudTask Center](https://github.com/cloudtask/cloudtask-center)
   
   
   
   
   Cloudtask的调度中心服务，通过中心服务计算任务的目标工作节点，负责集群节点发现管理、任务分配等工作。   
   中心服务按集群对工作节点进行管理，

   
- [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)
   
   Cloudtask的任务工作节点

#### 什么是 `Runtime` ?


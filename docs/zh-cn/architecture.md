### Cloudtask架构

![架构图](https://cloudtask.github.io/cloudtask/_media/cloudtask.png)

#### 系统角色

- [CloudTask Web](https://github.com/cloudtask/cloudtask-web)   
   
   Cloudtask的前端网站管理系统，可通过站点可视化管理任务信息、分组信息、集群信息和各项系统配置参数；   
   站点提供更直观的展示功能，比如 `Dashboard`、`Activity`、 `Task`、`Search Job`、`Runtime` 等功能模块。   
   通过 `Task` 任务列表可以按分组方式快速查看任务和日志信息。

- [CloudTask Center](https://github.com/cloudtask/cloudtask-center)
   
   Cloudtask的中心调度服务，负责管理集群所有在线工作节点的状态，并为每个 `Runtime` 维护一个任务分配表信息，同时保持   
   分配表数据的一致性。中心调度服务采用一致性 `hash` 算法来对任务进行动态分配，所有任务只会被分配到它所属 `Runtime`   
   的工作节点上调度执行。
      
- [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)
   
   Cloudtask的工作节点

#### 什么是 `Runtime` ?

   `runtime` 是指一组具有相同运行环境的工作节点集群，每一个工作节点在启动时都需要配置 `runtime` 名称属性，中心服务器在发现节点后会将这些具有相同 `runtime` 名称的节点组成一个 `hash` 环结构，`runtime` 下的所有任务在分配时，会按 `hash` 算法分配到该集群中的工作节点下。

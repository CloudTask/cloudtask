### Cloudtask架构

![架构图](_media/cloudtask.png)

#### 系统角色

- [CloudTask Web](https://github.com/cloudtask/cloudtask-web)   
   
   
   Cloudtask平台网站系统，通过站点可视化管理任务信息、分组信息、集群信息和系统配置；   
   站点提供更直观的展示功能，比如 `Dashboard`、`Activity`、`Search Job`、`Runtime` 等模块。   
   通过任务信息列表可以快速查看任务的执行日志和当前调度状态。  

- [CloudTask Center](https://github.com/cloudtask/cloudtask-center)
   
   Cloudtask的调度中心服务，通过中心服务计算任务的目标工作节点，负责集群节点发现、任务分配等工作。

   
- [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)
   
   Cloudtask的任务工作节点
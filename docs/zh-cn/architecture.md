### Cloudtask架构

![架构图](https://cloudtask.github.io/cloudtask/_media/cloudtask.png)

#### 系统角色

- [CloudTask Web](https://github.com/cloudtask/cloudtask-web)      
&nbsp;前端网站管理系统，可通过前端站点可视化管理平台各项任务信息、分组信息、集群信息和各项系统配置参数；站点提供更直观的展示功能，比如 `Dashboard`、`Activity`、 `Task`、`Search Job`、`Runtime` 等等功能模块。进入 `Task` 任务信息列表模块，可按分组方式展现各项任务当前状态和日志信息，也可对任务进行各项参数调整。

- [CloudTask Center](https://github.com/cloudtask/cloudtask-center)      
&nbsp;中心调度服务，负责整个平台所有任务的调度分配工作，同时维护管理 `Runtime` 中所有在线工作节点的生命状态。并在内存中为每个 `Runtime` 维护一个任务分配表信息，这些任务分配表数据存储在 `zookeeper` 的集群节点中，从调度上保持了分配表数据的一致性，中心调度服务采用一致性 `hash` 算法对任务动态分配，所有任务只会被分配到隶属 `Runtime` 的工作节点中，若工作节点出现宕机现象，中心调度服务会将异常节点的任务进行迁移分配，保证任务调度不受影响。   
      
- [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)      
&nbsp;工作节点服务，负责将任务在本地执行起来，并在执行结束后将结果反馈到中心调度服务。我们可以为每个 `Runtime` 部署`(1~n)`个工作节点，这些节点在同一 `Runtime` 下组成一个集群，并相互分担着这个 `Runtime` 下的所有任务；工作节点服务以被动方式监视着 `zookeeper` 中的任务分配表变化，一旦有新任务分配到当前节点，会立即根据任务的 `Schedule` 信息来计算任务的执行周期，然后按时执行起来。

#### 什么是 `Runtime` ?

> `Runtime` 是指一组具有相同运行环境的工作节点集群，每一个工作节点在启动时都需要配置 `Runtime` 名称属性，中心服务器在发现节点后会将这些具有相同 `Runtime` 名称的节点组成一个 `hash` 环结构，`Runtime` 下的所有任务在分配时，会按 `hash` 算法分配到该集群中的工作节点下。

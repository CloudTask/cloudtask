### Cloudtask Architecture

![架构图](https://cloudtask.github.io/cloudtask/_media/cloudtask.png)

#### Components roles

- [CloudTask Web](https://github.com/cloudtask/cloudtask-web)   
&nbsp;The cloudtask's frontend website management system can visualize various task information, grouping information, cluster information and various system configuration parameters of the management platform through frontend websites, this sites provided more intuitive display functions, such as `Dashboard`, `Activity`, `Task`, `Search Job`, `runtime` and other features modules. enter the tasks list module to display the current status and log information of each task in group mode, as well as adjust various parameters of the task.

- [CloudTask Center](https://github.com/cloudtask/cloudtask-center)   
&nbsp;The cloudtask's backend center scheduler, it is responsible for the scheduling and distribution of all tasks across the platform, while maintaining the management of the life status of all online worker nodes in cluster `runtime`, and maintain a tasks allocation table information in memory for each `runtime`, these tasks allocation table data are stored in the cluster node of `zookeeper`, the scheduler used a hash algorithm to ensure the consistency of the tasks allocation table, all tasks are only assigned to worker nodes that are part of `runtime`, If the work node goes down or restarts, the central dispatch service will migrate and allocate the tasks of the abnormal node to ensure that the task scheduling is not affected.
      
- [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)      
&nbsp;The cloudtask's work node service, it is responsible for executing the task locally and feeding the result back to the central dispatch service after the execution is completed. we can deploy `(1~n)` work nodes for each `runtime`. these nodes form a cluster under the same `runtime` and share all the tasks under `runtime`, the worker node monitoring the changes in the tasks allocation table in `zookeeper` cluster. when tasks are allocated, it will immediately calculate the task execution cycle based on the `schedule` information of the task and execute it on times.

#### What is `runtime` ?

> The `runtime` is a cluster composed of multiple working nodes with the same running environment, each worker node needs to configure the runtime name attribute before joining the cluster, the central dispatch service will discovery that they form a cluster based on `runtime`,all tasks under `runtime` are allocation to the work node in the cluster by the `hash` algorithm.
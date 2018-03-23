# CloudTask

![Logo](https://avatars0.githubusercontent.com/u/28881302?s=150&v=4)

### What is CloudTask?

`Cloudtask` is a lightweight distributed task scheduling management platform that is small and easy to used. we can upload, pause, start tasks, clone a task, modify the task's execution command, view the task's schedule info status and execution log, developers can write any task program in the programming language they are most familiar with. It can be a console program or a shell script and uploaded to the platform.

### CloudTask Design

The `Cloudtask` backend is a stateless service. It uses the `Zookeeper` to realize the discovery and coordination between the nodes of the cluster, and the nodes register to the registration center in a heartbeat manner, The center server manages all the clusters in a unified manner, tasks scheduling is distributed in clusters and has good scalability, the tasks allocation information is divided into clusters and the allocation data is stored in the `Zookeeper` node to maintain the consistency of the allocated data.

##### More info at [cloudtask.github.io](https://cloudtask.github.io/cloudtask)
##### Demo at [http://104.225.159.143:8091](http://104.225.159.143:8091)
### Architecture

![Architecture](./docs/_media/cloudtask.png)

### Features   
* Distributed: Use `Zookeeper` to implement registration center, distribution of nodes and tasks.
* Cross-platform: Supports deployment on `Windows` and `Linux` platforms.
* Task Timer: In strict accordance with the task scheduling cycle execution, the same task at the same time point will only be scheduled to trigger an execution to maintain consistency of state.
* Tasks Control: You can manually start and stop a task at any time.
* Parallel Scheduling: Each node in the cluster schedules local tasks in parallel.
* Elasticity Cluster: Nodes are gathered in a ring structure, adding and exiting the cluster is more flexible. The local part task is reassigned based on the node.
* Cluster Failover: When a node failure occurs, only the tasks of the failed node are migrated to prevent the cluster task from adjusting jitter.
* Operation Maintenance: Manage all tasks and cluster information centrally through front-end sites, including User authorization management.
* Event Notify: If the task fails to execute or the cluster node is offline, the system will notify the operator with the event email.
* Collecting Message: The node will feedback the tasks execution result to the center server, and the log display will be more intuitive.
* Stability Cluster: In the case of no server fluctuations, the task will not be allocation. 

### Components

* [CloudTask InitConfig](https://github.com/cloudtask/cloudtask-initconfig)
* [CloudTask Web](https://github.com/cloudtask/cloudtask-web)
* [CloudTask Center](https://github.com/cloudtask/cloudtask-center)
* [CloudTask Agent](https://github.com/cloudtask/cloudtask-agent)

## Preview

Web Login

![Login](https://cloudtask.github.io/cloudtask/_media/login.png)

Dashboard

![Dashboard](https://cloudtask.github.io/cloudtask/_media/dashboard.png)

Cluster

![Cluster](https://cloudtask.github.io/cloudtask/_media/runtime_states.png)

Tasks

![Tasks](https://cloudtask.github.io/cloudtask/_media/tasks_list.png)

## License

cloudtask source code is licensed under the [Apache Licence 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).   

### Task Scheduling
---

#### Node composition
The center service manages all nodes in the cluster in a ring structure. the composed clusters are divided by runtime. `N` nodes in each runtime form a hash ring structure. Once the node is successfully added to a `runtime`, it will start to share some of the tasks in `runtime`, not all nodes can be discovered by the center service service, provided that the node must be added to the front-end `Runtimes` management, otherwise even if the discovery node is found to be invalid, it is forbidden to join the cluster. 

#### Allocation Principle

> Default Alloc Mode  

In order to ensure a uniform runtime environment, all tasks will only be allocated in their own runtime worker nodes and will not be assigned to other runtimes. If all working nodes under `Runtime` are offline or closed, all tasks under the current `Runtime` will be in the wait for allocation state `reallocating`, otherwise the task will always be assigned to a node.

> Partial Alloc Mode

A subset of the default alloc mode, tasks can be assigned based on the selected `Target Server`, these servers all come from the same `runtime`, but the scope is smaller, the scheduler always allocates nodes in the selected server. if the servers in `Target Server` are offline or down, then the task will also be in the wait for allocation state `reallocating`.

#### Trigger Scheduling

When creating, modifying, or deleting a task, the scheduler immediately schedules the task once. In order to prevent the task from allocating jitter, it rarely changes after assigning a working node. If there is a work node online or offline, the scheduler will redistribute the task in `runtime` once. During the allocation process, if the task is being executed, it will be forced to terminate.

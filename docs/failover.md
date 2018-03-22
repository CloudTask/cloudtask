### Tasks Failover
---

The tasks migration is mainly to avoid the single point problem of the system, and does not hope that the task cannot be smoothly executed due to the failure of the work node.

The center service maintains the online status of all work nodes. If there is no response from the work node within the default `60s`, the system will consider this work node as abnormally disconnected, and all tasks assigned to it will be re-assigned once. After the node is back online, the task will be re-migrated back.

Note: If all working nodes in a `runtime` are offline, the task will not be migrated. All tasks states are `reallocating` until the working node goes online to trigger the migration.

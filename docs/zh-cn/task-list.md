# runtime列表

![Task Manage](../_media/manage_task.png)

> 列表头部

- `Status`：有`running`、`disabled`、`idle`、`reallocating`、`failed`五种状态，分别表示`正在执行`、`禁用`、`等待执行`、`等待分配`、`执行失败`，可以通过选择status筛选对应状态的任务
- `Add New Job`：点击页面上方`Add New Job`按钮来新建任务
- `Batch Update File`：批量更新当前组下的任务文件
![Batch Update File](../_media/batch_update.png)

> 列表体

- `Status`: 任务状态
- `Job Name`: 任务名
- `Next Run Time`: 任务下次执行时间
- `Last Run Time`: 任务上次执行时间
- `Last Result`: 上次执行结果

列表最后一列可以对任务进行操作：
- `Start`: 强制让任务执行一次，正在执行的任务不可用
- `Stop`: 强制让任务停止，只有正在执行的任务可用
- `Disabled`：禁用任务，禁用后的任务将不再分配执行 
- `Enabled`: 启用任务，启用后的任务将重新分配执行
- `Logs`：查看任务日志
- `Download File`：下载当前任务的执行文件，当前任务有执行文件时显示此选项
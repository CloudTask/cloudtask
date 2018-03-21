### 常见问题

> 当一个任务执行异常退出后，在执行日志中怎么无法看到任务的异常情况？

当一个任务执行发生异常时，我们需要正确捕获异常并返回给 `cloudtask`，然后中心服务器会记录到任务日志中，若不捕获，系统只会得到任务进程返回的 `exitcode` 码，我们在编写任何一个任务程序或脚本时，都需要按如下方式来捕获异常，这样在日志中才能显示异常堆栈信息，cloudtask 日志内容是以任务的 `stdout` 和 `stderr` 来收集的，所以我们的一切输出需要按系统标准来编写。

``` csharp
try
{
    //do something...
}
catch (Exception err)
{
    //使用Console.Error是将异常信息输出到错误流（errout）中，表明是错误输出
    Console.Error.WriteLine(err.Message);
    Console.Error.WriteLine(err.StackTrace);
    if (err.InnerException != null)
    {
        Console.Error.WriteLine("Inner Exception:");
        Console.Error.WriteLine(err.InnerException.Message);
        Console.Error.WriteLine(err.InnerException.StackTrace);
    }

    System.Environment.Exit(-1); //告诉cloudtask，这个不是正确的退出。
}
```

> 工作节点服务有两张网卡，启动加入到 `Runtime` 后，无法看到当前工作节点服务在线？

工作节点服务器启动后会将本地的IP地址一起注册到中心调度服务器，在启动时本地配置里默认没有选用指定的网络地址，此时会随机选择一个网卡地址来注册到中心，而选择的网卡地址与前端站点配置的IP地址不一致导致。

``` yaml
version: v.2.0.0
cluster:
    hosts: 192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181
    root: /cloudtask
    device: # 启动时，本地默认是没有设置网卡IP地址的，若是多网卡我们需要设置一个IP地址或网卡设备名称即可。
    runtime: myCluster
    pulse: 8s
    threshold: 1
```
例如：
``` yaml
    device: 192.168.2.80 #或 eth0 
```
> 为什么我部署的所有任务状态全是 `reallocating` 状态？

当一个 `runtime` 下大部分任务出现 `reallocating` 状态，一般是因为服务发现工作节点服务器，不能再分配任务到工作服务节点上了，需要检查工作节点服务是否已经全部宕机或重启造成。   

若部分任务出现 `reallocating` 状态，我们需要检查任务的 `Target Server` 设置，检查设置的服务器是否已经不在线或宕机了，启动后即可再分分配任务。

> 为什么我 `start` 一个 job 不起作用？ 

工作节点服务器启动注册到中心调度服务后，在工作节点上会默认启动 `8600` 端口，我们可以访问看是否已启动：

```bash
$ cutl http://192.168.2.80:8600/cloudtask/v2/_ping
```
当中心调度器接收到来至站点请求： `start`、`stop` 等命令后，会转发到工作节点服务 `8600` 这个端口，所以我们需要确认工作节点本地是否存在防火墙未开放该端口，或是端口已被另外的服务占用，我们可以修改 `config.yaml` 中的默认端口来再次启动。

可以修改配置文件中 `api` 节点下的 `hosts` 值，`[:8600]` 是默认绑定所有网卡.
``` yaml
api:
    hosts: [":8600"]
    enablecors: true
```

> 以 `docker` 容器方式部署的工作节点服务无法执行我的任务？

该问题一般为容器内缺少运行环境导致，我们官方默认制作的镜像：`docker.io/cloudtask/cloudtask-agent:2.0.0` 是基于 `centos 7` 基础镜像制作的，若要运行 `java`、 `python` 等需要运行时的任务，请自己制作具备这些运行时的工作节点镜像后再部署, 在 `cloudtask-agent` 项目中有 `Dockerfile` 参考，可以修改基础镜像依赖，原则上我们要保证 `Runtime` 下的工作节点服务器运行环境一致，这样才能保证任务能正常调度执行。

``` bash
FROM centos:7 #请修改基础镜像制作自己的环境

MAINTAINER bobliu bobliu0909@gmail.com

RUN mkdir -p /opt/cloudtask/etc

RUN mkdir -p /opt/cloudtask/cache

RUN mkdir -p /opt/cloudtask/logs

COPY etc /opt/cloudtask/etc

COPY cloudtask-agnet /opt/cloudtask/cloudtask-agent

WORKDIR /opt/cloudtask

VOLUME ["/opt/cloudtask/etc"]

VOLUME ["/opt/cloudtask/cache"]

VOLUME ["/opt/cloudtask/logs"]

CMD ["./cloudtask-agent"]

EXPOSE 8600
```



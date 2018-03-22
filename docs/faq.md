### Common Problems
---
> How do you see the exception log when the task execution exits abnormally?

&nbsp;&nbsp;When a task exits abnormally, it needs to catch the exception and return it to `cloudtask`, then the center service will record it in the task log, If you do not capture, the system will only get the `exitcode` code returned by the task process, When we write any task program or script, we need to catch exceptions as follows, will display exception stack information in the log.

``` csharp
try
{
    //do something...
    Console.WriteLine("Hello Job."); //stdout to cloudtask log
}
catch (Exception err)
{
    Console.Error.WriteLine(err.Message); //stderr to cloudtask log
    Console.Error.WriteLine(err.StackTrace); //stderr to cloudtask log
    if (err.InnerException != null)
    {
        Console.Error.WriteLine("Inner Exception:");
        Console.Error.WriteLine(err.InnerException.Message);
        Console.Error.WriteLine(err.InnerException.StackTrace);
    }
    System.Environment.Exit(-1); //abnormal exit.
}
```

> Work node service has two network adapters. after starting to join the runtime cluster, you cannot see the current working node services online.

&nbsp;&nbsp;After the work node server starts up, it registers with the center service using the local IP address. The specified network address was not selected in the local configuration. By default, one network adapter address is randomly selected to register with the center, which is inconsistent with the IP address configured on the front-end site.

``` yaml
version: v.2.0.0
cluster:
    hosts: 192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181
    root: /cloudtask
    device: # select network address or device name, default random.
    runtime: myCluster
    pulse: 8s
    threshold: 1
```
&nbsp;&nbsp;such as:
``` yaml
    device: 192.168.2.80 # or eth0 
```

&nbsp;&nbsp;After modifying the configuration and rebooting, you can access the center service API to view the online work node service status of a `Runtime`. The data in `servers` is the online service node.

``` bash
$ curl http://192.168.2.80:8985/cloudtask/v2/runtimes/myCluster/servers

Response 200 OK
{
    "content": "request successed.",
    "data": {
        "servers": [
            {
                "key": "509794cc-3539-4f58-a4d3-cc02a4f4848f",
                "name": "host.localdomain",
                "ipaddr": "192.168.2.80",
                "apiaddr": "http://192.168.2.80:8600",
                "os": "linux",
                "platform": "amd64",
                "status": 1,
                "alivestamp": 1521276530
            },
            {
                "key": "517ae088-779c-4520-b23f-e5f0c341081d",
                "name": "localhost.localdomain",
                "ipaddr": "192.168.2.81",
                "apiaddr": "http://192.168.2.81:8600",
                "os": "linux",
                "platform": "amd64",
                "status": 1,
                "alivestamp": 1521277792
            }
        ]
    }
}
```

> Why is all the tasks I deploy to be "reallocating" state?

&nbsp;&nbsp;When most tasks are in the `reallocating` state during a run, generally because the central scheduling service can no longer assign tasks to the work node, we need to check if the worker node is completely shut down or is being restarted.

&nbsp;&nbsp;If some tasks are "reallocating" state, you need to check the "target server" of the task to check whether the set server is offline or closed.

> Why can't I click start to start a task?

&nbsp;&nbsp;After the work node starts registering to the center service, the `8600` port is started by default. We can access to check if it is started:

```bash
$ cutl http://192.168.2.80:8600/cloudtask/v2/_ping
```

&nbsp;&nbsp;When it is inaccessible, the local firewall may not allow access to the port or the port or be occupied by another service. you can modify `config.yaml` to replace a port

``` yaml
api:
    hosts: [":8600"]
    enablecors: true
```

> Work node services deployed as `docker` containers cannot perform my task?

&nbsp;&nbsp; Container lacks runtime environment, our official default image: `docker.io/cloudtask/cloudtask-agent:2.0.0` is based on the `centos 7` base image, if you want to run `java`, `python` and other runtime task programs, please create these environments to create a working node docker image, there is a `Dockerfile` reference in the `cloudtask-agent` project.

``` bash
FROM centos:7 # please modify the base image to create your own environment

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



### Cloudtask安装部署

#### 环境依赖

在部署 `Cloudtask` 前首先需要搭建起 `zookeeper` 和 `mongodb` 这两个第三方工具来支撑 Cloudtask 平台，若要保证高可用，需要以集群方式搭建。 

- [Zookeeper cluster 3.4.6+](https://zookeeper.apache.org)   

- [Mongodb cluster 3.0.12+](https://www.mongodb.com)  

#### 初始化配置 

- [Cloudtask InitConfig](https://github.com/cloudtask/cloudtask-initconfig)

我们在部署 `Cloudtask` 的各个 `Components` 时，会遇到一些共用的配置参数，若分别为每个服务单独配置会过于繁琐，所以我们需要将配置信息提交到 `zookeeper` 节点中保存，各个服务启动时只需要连接到 `zookeeper` 读取配置节点数据即可。   
`Cloudtask InitConfig` 工具可以为我们完成这项工作，首先准备好 `ServerConfig.json` 配置文件，填入自己的集群配置信息，然后执行 `cloudtask-initconfig` 来完成初始化工作。

> ServerConfig.json

在配置时需要注意 `websitehost` 与 `centerhost` 两项参数：

- websitehost: 表示 `cloudtask-web` 的站点地址和端口，默认端口为：`8091`.
- centerhost: 表示 `cloudtask-center` 中心调度服务的地址和端口，默认端口为：`8985`.

``` json
{
    "zookeeper": {
        "hosts": "192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181",
        "root": "/cloudtask"
    },
    "serverconfig": {
        "websitehost": "http://192.168.2.80:8091",
        "centerhost": "http://192.168.2.80:8985",
        "storagedriver": {
            "mongo": {
                "hosts": "192.168.2.80:27017,192.168.2.81:27017,192.168.2.82:27017",
                "database": "cloudtask",
                "auth": {
                    "user": "datastoreAdmin",
                    "password": "ds4dev"
                },
                "options": [
                    "maxPoolSize=20",
                    "replicaSet=mgoCluster",
                    "authSource=admin"
                ]
            }
        }
    }
}
```

> 开始执行

``` bash
$ ./cloudtask-initconfig -f ./ServerConfig.json
```

> 成功输出

``` bash
2018/03/16 15:07:29 Connected to 192.168.2.80:2181
2018/03/16 15:07:29 Authenticated: id=99692315792834560, timeout=15000
2018/03/16 15:07:29 Re-submitting `0` credentials after reconnect
zookeeper path: /cloudtask/ServerConfig
2018/03/16 15:07:30 Recv loop terminated: err=EOF
serverconfig: {"websitehost":"192.168.2.80:8091","centerhost":"192.168.2.80:8985","storagedriver":{"mongo":{"auth":{"password":"ds4dev","user":"datastoreAdmin"},"database":"cloudtask","hosts":"192.168.2.80:27017,192.168.2.81:27017,192.168.2.82:27017","options":["maxPoolSize=20","replicaSet=mgoCluster","authSource=admin"]}}}
2018/03/16 15:07:30 Send loop terminated: err=<nil>
initconfig to zookeeper successed!
```

#### 部署中心调度服务 

- [Cloudtask Center](https://github.com/cloudtask/cloudtask-center) 



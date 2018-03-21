### Cloudtask部署

#### 环境依赖

在部署 `Cloudtask` 前首先需要搭建起 `zookeeper` 和 `mongodb` 这两个第三方工具来支撑 Cloudtask 平台，若要保证高可用，需要以集群方式搭建。 

- [Zookeeper cluster 3.4.6+](https://zookeeper.apache.org)   

- [Mongodb cluster 3.0.12+](https://www.mongodb.com)  

#### 初始化配置 

> [Cloudtask InitConfig](https://github.com/cloudtask/cloudtask-initconfig)

我们在部署 `Cloudtask` 的各个 `Components` 时，会遇到一些共用的配置参数，若分别为每个服务单独配置会过于繁琐，所以我们需要将配置信息提交到 `zookeeper` 节点中保存，各个服务启动时只需要连接到 `zookeeper` 读取配置节点数据即可。   
`Cloudtask InitConfig` 工具可以为我们完成这项工作，首先准备好 `ServerConfig.json` 配置文件，填入自己的集群配置信息，然后执行 `cloudtask-initconfig` 来完成初始化工作。

> ServerConfig.json

在配置时需要注意 `websitehost` 与 `centerhost` 两项参数：

- websitehost: 表示 `cloudtask-web` 的站点地址和端口，默认端口为：`8091`.
- centerhost: 表示 `cloudtask-center` 中心调度服务的地址和端口，默认端口为：`8985`.
- zookeeper: 提前配置的 `zookeeper` 集群信息，`root` 默认为 /cloudtask 节点.
- storagedriver: 默认支持 `mongodb` 存储驱动，配置好 `mongodb` 的各项参数，若没有账户密码，则不需要 `auth` 键.

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

#### 启动中心调度服务 

> [Cloudtask Center](https://github.com/cloudtask/cloudtask-center) 

中心调度服务是一个无状态服务，启动后以集群中心角色负责整个平台的任务调度，同时还负责集群中工作节点动态发现管理；中心调度服务若停止，则无法分配新任务或迁移故障节点的现有任务，但并不会影响现有已分配到工作节点上的任务，只是无法收集最新的执行日志和状态。

> 修改配置

  在启动中心调度服务前，首先打开配置文件 `config.yaml` 并定位到 `cluster` 节点，需要先修改配置中的 `hosts` 和 `root` 这两个键值，并保持与初始化配置文件 `ServerConfig.json` 中 `zookeeper` 节点的配置一致即可。若想更换启动端口，可修改 `config.yaml` 中的 `api` 节点。


> 程序启动

``` bash
$ ./cloudtask-center -f ./etc/config.yaml
```

> docker方式启动

``` bash
$ docker run -d --net=host --restart=always \
  -v /opt/app/cloudtask-center/etc/config.yaml:/opt/cloudtask/etc/config.yaml \
  -v /opt/app/cloudtask-center/logs:/opt/cloudtask/logs \
  -v /etc/localtime:/etc/localtime \
  --name=cloudtask-center \
  cloudtask/cloudtask-center:2.0.0
```
> 检查服务

服务启动后，默认服务端口为：`8985`，若能访问则证明部署成功。

``` bash
$ curl http://192.168.2.80:8985/cloudtask/v2/_ping

Response 200 OK
{
	"app": "296721666eddf741016105ebe1bc3fb4",
	"key": "3a95a871-9dc4-4955-b213-5a040e324309",
	"node": {
		"type": 1,
		"hostname": "host.localdomain",
		"datacenter": "",
		"location": "",
		"os": "linux",
		"platform": "amd64",
		"ipaddr": "192.168.2.80",
		"apiaddr": "http://192.168.2.80:8985",
		"pid": 1,
		"singin": false,
		"timestamp": 0,
		"alivestamp": 1521268194,
		"attach": null
	},
	"systemconfig": {
		"version": "v.2.0.0",
		"pidfile": "./jobserver.pid",
		"retrystartup": true,
		"useserverconfig": true,
		"cluster": {
			"hosts": "192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181",
			"root": "/cloudtask",
			"device": "",
			"runtime": "",
			"os": "",
			"platform": "",
			"pulse": "30s",
			"timeout": "60s",
			"threshold": 1
		},
		"api": {
			"hosts": [
				":8985"
			],
			"enablecors": true
		},
		"scheduler": {
			"allocmode": "hash",
			"allocrecovery": "320s"
		},
		"cache": {
			"lrusize": 1024,
			"storagedriver": {
				"mongo": {
					"database": "cloudtask",
					"hosts": "192.168.2.80:27017,192.168.2.81:27017,192.168.2.82:27017",
					"options": [
						"maxPoolSize=20",
						"replicaSet=mgoCluster",
                        "authSource=admin"
					]
				}
			}
		}
	}
}
```

#### 启动站点服务

> [Cloudtask Web](https://github.com/cloudtask/cloudtask-web) 

首先修改站点配置文件：`src/server/common/config.js`，将 `zookeeperConfig` 变量修改为初始化配置文件 `ServerConfig.json` 中 `zookeeper` 节点的 'hosts' 配置即可，若是以 `docker` 容器方式启动，可以新创建一个 `config.js` 文件再映射到容器中。

``` javascript
const path = require('path');

var listenPort = 8091;
var uploadFolder = path.join(__dirname, '../uploads');
var zookeeperConfig = '192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181';
module.exports = {
    listenPort,
    uploadFolder,
    zookeeperConfig
}
```

> node启动

``` bash
$ git clone https://github.com/cloudTask/cloudtask-web.git
$ cd cloudtask-web
$ npm install
$ npm start
```

> docker方式启动

``` bash
$ docker run -d --net=host --restart=always \
  -v /opt/app/cloudtask-web/config.js:/cloudtask-web/common/config.js \
  -v /opt/app/cloudtask-web/uploads:/cloudtask-web/uploads \
  -v /etc/localtime:/etc/localtime \
  --name=cloudtask-web \
  cloudtask\cloudtask-web:2.0.0 
```
> 访问网站

启动成功后访问：http://localhost:8091，默认管理账户：admin，登录密码：123456   

![Cloudtask-Web](https://raw.githubusercontent.com/CloudTask/cloudtask-web/master/screenshots/login.png)

#### 启动工作节点

一个工作节点只能加入到一个 `Runtime` 中，当多个相同IP地址的工作节点加入到一个相同的 `Runtime` 中时，只有最先加入的工作节点才会被加入集群调度。   
工作节点启动成功后，需要在站点 `Runtime` 管理中创建这个工作节点加入的 `Runtime` 信息，若已存在，则在 `Runtime` 中的 `Servers` 属性中添加这个工作节点的IP地址或 ServerName 即可，若在站点 `Runtime` 中不添加已经启动的工作节点信息，节点就不会被加入集群。

> 修改配置

  在启动工作节点前，首先打开配置文件 `config.yaml` 并定位到 `cluster` 节点，需要先修改配置中的 `hosts` 和 `root` 这两个键值，并保持与初始化配置文件 `ServerConfig.json` 中 `zookeeper` 节点的配置一致即可。   
  同样在 `cluster` 节点中，最重要的还需要修改 `runtime` 属性，设置我们想加入的目标 `Runtime` 名称，这个名称可能在站点 `Runtime` 管理中已经添加了，若未添加可以待工作节点启动后再去创建。

> 程序启动

``` bash
$ ./cloudtask-agent -f ./etc/config.yaml
```

> docker方式启动

``` bash
$ docker run -d --net=host --restart=always \
  -v /opt/app/cloudtask-agent/etc/config.yaml:/opt/cloudtask/etc/config.yaml \
  -v /opt/app/cloudtask-agent/logs:/opt/cloudtask/logs \
  -v /opt/app/cloudtask-agent/cache:/opt/cloudtask/cache \
  -v /etc/localtime:/etc/localtime \
  --name=cloudtask-agent \
  cloudtask/cloudtask-agent:2.0.0
```

> 检查服务

服务启动后，默认端口为：`8600`，若能访问则证明部署成功。

``` bash
$ curl http://192.168.2.80:8600/cloudtask/v2/_ping

Response 200 OK
{
    "app": "7969c05a25a3cdfd39645bba2d213173",
    "key": "509794cc-3539-4f58-a4d3-cc02a4f4848f",
    "node": {
        "type": 2,
        "hostname": "host.localdomain",
        "datacenter": "",
        "location": "myCluster",
        "os": "linux",
        "platform": "amd64",
        "ipaddr": "192.168.2.80",
        "apiaddr": "http://192.168.2.80:8600",
        "pid": 1,
        "singin": true,
        "timestamp": 1521546613,
        "alivestamp": 1521276530,
        "attach": "eyJqb2JtYXhjb3VudCI6MjU1fQo="
    },
    "systemconfig": {
        "version": "v.2.0.0",
        "pidfile": "./jobworker.pid",
        "retrystartup": true,
        "useserverconfig": true,
        "centerhost": "http://192.168.2.80:8985",
        "websitehost": "http://192.168.2.80:8091",
        "cluster": {
            "hosts": "192.168.2.80:2181,192.168.2.81:2181,192.168.2.82:2181",
            "root": "/cloudtask",
            "device": "",
            "runtime": "myCluster",
            "os": "",
            "platform": "",
            "pulse": "8s",
            "threshold": 1
        },
        "api": {
            "hosts": [
                ":8600"
            ],
            "enablecors": true
        },
        "cache": {
            "maxjobs": 255,
            "savedirectory": "./cache",
            "autoclean": true,
            "cleaninterval": "30m",
            "pullrecovery": "300s"
        },
        "logger": {
            "logfile": "./logs/jobworker.log",
            "loglevel": "info",
            "logsize": 20971520
        }
    },
    "cache": {
        "allocversion": 48,
        "jobstotal": 3
    }
}
```
> 访问站点，创建 `Runtime`，检查节点状态

示例：如下3个工作节点分别在配置中设置了 `runtime` 为：`myCluster`，以管理员身份登录站点后，进入 `Manage` -> `Runtimes` -> `Add Runtime`，添加工作节点服务器后，可以立即看到工作节点已经正常加入在线。

创建 `myCluster`   
![架构图](https://cloudtask.github.io/cloudtask/_media/add_runtime_servers.png)

检查 `myCluster`   
![架构图](https://cloudtask.github.io/cloudtask/_media/runtime_servers.png)
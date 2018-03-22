### Cloudtask Deploy

#### Dependencies

Before deployment, you need to keep the `zookeeper` and `mongodb` environments running to support the Cloudtask platform, To maintain high availability, you need to running in a cluster. 

- [Zookeeper cluster 3.4.6+](https://zookeeper.apache.org)   

- [Mongodb cluster 3.0.12+](https://www.mongodb.com)  

#### Initialize Configuration

> [Cloudtask InitConfig](https://github.com/cloudtask/cloudtask-initconfig)

When deploying the `Components` of cloudtask, we will encounter some common configuration parameters, if we configure them separately for each service, it will be too tedious. so we need to submit the configuration information to the `zookeeper` nodes to save it, when each service starts, it only needs to connect to `zookeeper` to read configuration node data.

The `Cloudtask InitConfig` tool can do this for us. First, prepared a `ServerConfig.json` file, fill in your own cluster configuration information, and then execute `cloudtask-initconfig` to complete the initialization.

> ServerConfig.json

Need to pay attention to `websitehost` and `centerhost` two parameters, It determines the address and port that will need to be deployed in the future for the cloudtask site and the central scheduler.

- websitehost: site address and port of `cloudtask-web` component, default port:`8091`.
- centerhost: host address and port of `cloudtask-center` component, default port:`8985`.
- zookeeper: `zookeeper` cluster configures, `root` path default is `/cloudtask`.
- storagedriver: the default is the `mongodb` storage driver, which configures the parameters of `mongodb`. If there is no account password, the `auth` key is not needed.

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

> Start Cloudtask InitConfig Component

``` bash
$ ./cloudtask-initconfig -f ./ServerConfig.json
```

> Output Successed

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

#### Cloudtask Center Service

> [Cloudtask Center](https://github.com/cloudtask/cloudtask-center) 

The center service is a stateless service. after the started, responsible for the tasks scheduling of the entire platform. it is also responsible for the dynamic discovery management of the worker nodes in the cluster; if the center service stopped, the new tasks cannot be assigned or the node that fails to migrate is displayed. there are tasks, but it does not affect the existing tasks that has been assigned to the worker node, but it is unable to collect the latest execution log and status.

> Settings Configuration

Open the `config.yaml` file and navigate to the `cluster` node. you need to modify the `hosts` and `root` keys in the configuration and keep them consistent with the `zookeeper` in the initialization configuration file `ServerConfig.json`, to replace the API port, modify the `api` node.

> Start Cloudtask Center Component

``` bash
$ ./cloudtask-center -f ./etc/config.yaml
```

> Docker 

``` bash
$ docker run -d --net=host --restart=always \
  -v /opt/app/cloudtask-center/etc/config.yaml:/opt/cloudtask/etc/config.yaml \
  -v /opt/app/cloudtask-center/logs:/opt/cloudtask/logs \
  -v /etc/localtime:/etc/localtime \
  --name=cloudtask-center \
  cloudtask/cloudtask-center:2.0.0
```

> Check Service

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
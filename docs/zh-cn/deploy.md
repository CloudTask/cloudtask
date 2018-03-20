### Cloudtask安装部署

#### 环境依赖

在部署 `Cloudtask` 前首先需要搭建起 `zookeeper` 和 `mongodb` 第三方工具来支撑 Cloudtask 平台，若要保证高可用，需要以集群方式搭建。 

- [Zookeeper cluster 3.4.6+](https://zookeeper.apache.org)   

- [Mongodb cluster 3.0.12+](https://www.mongodb.com)  

#### 初始化配置

我们在部署 `Cloudtask` 的各个 `Components` 时，会遇到很多共用的配置参数，若分别为每个服务单独配置会过于繁琐，所以我们需要将配置信息提交到 `zookeeper` 节点中保存，各个服务启动时只需连接到 `zookeeper` 读取配置节点即可。   
`Cloudtask InitConfig` 工具可以为我们完成这项工作，首先准备好 `ServerConfig.json` 配置文件，填入自己的集群配置信息，然后执行 `Cloudtask InitConfig` 完成初始化。

- [Cloudtask InitConfig](https://github.com/cloudtask/cloudtask-initconfig) 



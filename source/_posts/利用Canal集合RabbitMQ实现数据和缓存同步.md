title: 利用Canal集合RabbitMQ实现数据和缓存同步
author: OdinSam
tags:
  - Mysql
  - .Net Core
  - Canal
  - RabbitMQ
categories:
  - .Net Core
  - RabbitMQ
  - MySql
  - Canal
  - ''
abbrlink: a3b9
date: 2021-06-09 05:34:00
---
> Canal的主要用途是基于 MySQL 数据库增量日志解析，提供增量数据订阅和消费。他可以模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送 dump 协议。MySQL master收到dump请求，开始推送 binary log 给 slave (即canal)，canal解析binary log 对象(原始为 byte 流)。

<!-- more -->

#### Canal介绍

> Canal 的 Github：https://github.com/alibaba/canal 里边有详细介绍说明以及安装方法。具体不在叙述。

#### MySql改动

> 首先需要给mysql创建对应的canal用户

```sql
CREATE USER canal IDENTIFIED BY 'canal';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
FLUSH PRIVILEGES;
```
> 其次修改 my.cnf 文件并 <font color="red">重启数据库</font>

```ini
[mysqld]
log-bin=mysql-bin # 开启 binlog
binlog-format=ROW # 选择 ROW 模式
server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复
```

#### Canal配置

> Canal单机环境(开发代码测试)主要配置两个文件，分别是 conf/canal.properties 文件和 conf/example/instance.properties 文件。具体配置如下:

  canal.properties

```config
##################################################################################################
####### 这部分结构是配置文件自带的 只需要写清楚即可，其中 exchange 为 rabbitMQ的exchange的名字
####### username 和 password 是 rabbitMQ 的 用户名和密码 (我自己新建了一个rabbitMQ用户)
##################################################################################################
    
#########                   RabbitMQ         #############
##################################################
rabbitmq.host = 127.0.0.1
rabbitmq.virtual.host = /
rabbitmq.exchange = canal-exchange
rabbitmq.username = canalConsumer
rabbitmq.password = canalConsumer
rabbitmq.deliveryMode =
```
   
   最为主要的是要找到配置文件中 <font color="red">canal.serverMode = rabbitMQ</font> 他的默认值是 tcp 切记要改为 <font color="red">rabbitMQ</font>


	
  instance.properties

```config
canal.instance.master.address=127.0.0.1:3306  #数据库的 ip:port
    
canal.instance.dbUsername=canal 	#数据库的 用户名
canal.instance.dbPassword=173Canal~	#数据库的 密码 
    
canal.instance.tsdb.dir=${canal.file.data.dir:../conf}/${canal.instance.destination:}
canal.instance.tsdb.url=jdbc:h2:${canal.instance.tsdb.dir}/h2;CACHE_SIZE=1000;MODE=MYSQL;
#canal.instance.tsdb.url=jdbc:mysql://127.0.0.1:3306/canal_tsdb
    
# canal.instance.filter.regex=.*\\..*	# https://github.com/alibaba/canal/wiki/AdminGuide 搜索 canal.instance.filter.regex 有详细说明
canal.instance.filter.regex=db_OdinOIS.Logs  # 要监控的库和表  https://github.com/alibaba/canal/wiki/AdminGuide 搜索 
canal.instance.filter.regex 有详细说明
    
canal.mq.topic=canal-routingkey # rabbitMQ 创建 queues 时的 routing key 的值
```

#### 数据解析

> 至此如果以上步骤都没有问题的话，执行 ./bin/startup.sh 启动，并在你监控的表中设置增量数据，rabbitMQ 就会有对应数据。获取到的数据为json格式，具体说明如下:

```json
{
	// data内为获取到的增量数据 key是数据库对应的字段 value是数据库的值
	"data": [{
		"id": "1487",
		"Timestamp": "2021-06-09 05:23:12.538+08:00",
		"Level": "Information",
		"Message": "Entity Framework Core",
		"Exception": null,
		"Properties": "",
		"_ts": null
	}],
	// 库名
	"database": "db_OdinOIS",
	"es": 1623187400000,
	"id": 1,
	"isDdl": false,
	// 字段对应mysql的数据类型
	"mysqlType": {
		"id": "int",
		"Timestamp": "varchar(100)",
		"Level": "varchar(15)",
		"Message": "text",
		"Exception": "text",
		"Properties": "text",
		"_ts": "timestamp"
	},
	// 如果是 update 操作这里会是更新前的数据
	"old": null,
	// 主键
	"pkNames": ["id"],
	"sql": "",
	"sqlType": {
		"id": 4,
		"Timestamp": 12,
		"Level": 12,
		"Message": 2005,
		"Exception": -4,
		"Properties": 2005,
		"_ts": 93
	},
	// 表名字
	"table": "Logs",
	// 时间戳
	"ts": 1623187530269,
	// 操作类型
	"type": "INSERT"
}
```
   
这样我们就可以利用代码从 rabbitMQ 中消费对应的信息，然后再做其他操作。当然也可以利用 Canal 结合 redis 实现 mysql 和缓存数据同步，在利用CacheManager类库，利用redis做挡板，就可以同时实现 redis 缓存和内存缓存同步。这样整个分布式项目就可以实现读写分离、缓存同步。
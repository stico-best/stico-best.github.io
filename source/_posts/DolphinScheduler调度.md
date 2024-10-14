---
title: DolphinScheduler调度
abbrlink: d7191e50
date: 2024-09-02 10:46:30
tags:
---

**记录——数仓DolphinScheduler调度工具使用**

因为可能涉及公司内部的数仓、客户保单数据，所以我在自己电脑搭了类似的虚拟机，部署了Hadoop、Hive、Spark、Sqoop、zookeeper、DS等等组件，模拟效果演示，就不放出项目环境的截图，只拿自己虚拟机模拟部分流程和数据，只封装了ODS层的脚本作为工作流部署，重在熟悉部署工作流的流程以免忘了咋用。

<!--more-->

------

# DS部署与启动

虚拟机搭建了三台节点，组件都搭建完成，集体启动zookeeper，等待zk分配leader和follower

```markdown
三个节点都执行：
/export/server/zookeeper/bin/zkServer.sh start

查看状态：（一个leader 两个follower）
/export/server/zookeeper/bin/zkServer.sh status
```

{% asset_img 1.png  %}



首次DS需安装并启动

```markdown
sh /export/server/dolphinscheduler/install.sh
```

后续DS直接命令启动

```markdown
cd /export/server/dolphinscheduler_install

一键停止集群所有服务
sh ./bin/stop-all.sh
一键开启集群所有服务
sh ./bin/start-all.sh

单独停止和启动命令:
sh ./bin/dolphinscheduler-daemon.sh start master-server
sh ./bin/dolphinscheduler-daemon.sh stop master-server

sh ./bin/dolphinscheduler-daemon.sh start worker-server
sh ./bin/dolphinscheduler-daemon.sh stop worker-server

sh ./bin/dolphinscheduler-daemon.sh start api-server
sh ./bin/dolphinscheduler-daemon.sh stop api-server

sh ./bin/dolphinscheduler-daemon.sh start logger-server
sh ./bin/dolphinscheduler-daemon.sh stop logger-server

sh ./bin/dolphinscheduler-daemon.sh start alert-server
sh ./bin/dolphinscheduler-daemon.sh stop alert-server
```

访问DS: http://192.168.88.161:12345/dolphinscheduler	用户名: admin	密码: dolphinscheduler123



DS的架构：

{% asset_img 2.png  %}

------

# DS工作流编写流程

## 编写基于Sqoop导入ODS层的Shell脚本

_01_insurance_import_ods_prem_cv_real.sh（示例）：

```shell
#!/bin/bash

/export/server/sqoop/bin/sqoop import \
--connect jdbc:mysql://node1:3306/insurance \
--username root \
--password 123456 \
--table area \
--hive-import \
--hive-overwrite \
--hive-database  insurance_ods \
--hive-table area \
--fields-terminated-by '\t' \
-m 1
```

以上只是虚拟机环境整一次Sqoop import全量导入作为示例，**记得所有封装好的脚本分发给所有节点的统一路径**

{% asset_img 3.png  %}



## 创建项目及工作流

示例中任务优先级、告警等设置，需要根据项目开发人员规范要求来配置。

“脚本”处，记得写好 执行shell的命令，例中用dos2unix先转个义，防止识别出问题

{% asset_img 4.png  %}



## 配置定时任务调度

{% asset_img 5.png  %}



## 工作流实例查看

{% asset_img 6.png  %}



经过以上测试数据测试，首页查看全局下的任务、工作流状态统计：

{% asset_img 7.png  %}














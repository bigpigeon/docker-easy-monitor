### 引言

这是一个可定制的监控系统，目的就是为了让大家可以有针对性的对数据进行监控


该监控后台使用grafana，配置表格非常简单，只需简单一个sql语句即可，并且佩带sql语法补全，所以也不用当心忘记忘记字段名


数据库用的是influxdb，用来收集日志非常好用，在插入时就自动带上时间轴


### 开始

**前置条件**

- 安装最新版的docker和docker-compose


- root环境或者root下执行docker的环境


**安装**

1. 进入项目根目录

        cd docker-easy-monitor

2. 复制custom-compose.yml.temp到custom-compose.yaml并修改其中的环境变量

        cp custom-compose.yml.temp custom-compose.yaml

3. 进入项目根目录,执行以下脚本拉取镜像

        ./boot.sh pull

4. (可以跳过)导入grafana的配置文件，放入项目根目录下grafana中(以后考虑做成app的形式)

        scp -r ftp@you-archive-server:/your/grafana/dir grafana

5. 启动项目

        ./boot.sh up -d

6. 在浏览器访问grafana，并用帐号名**admin**刚才设置的密码环境变量**GF_SECURITY_ADMIN_PASSWORD**登录,

    http://your-server-name:3000


### grafana监控设置

使用http请求创建一个demo数据库

    curl -i -XPOST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE demo"


往influxdb插入几条测试数据

    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.61 1490166326000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.75 1490166386000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.77 1490166446000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.52 1490166506000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.3 1490166566000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.25 1490166626000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.62 1490166686000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.53 1490166746000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.42 1490166806000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.9 1490166866000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.82 1490166926000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server01,region=us-west value=0.77 1490166986000000000'

    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.061 1490166326000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.075 1490166386000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.077 1490166446000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.052 1490166506000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.03 1490166566000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.025 1490166626000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.062 1490166686000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.053 1490166746000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.042 1490166806000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.09 1490166866000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.082 1490166926000000000'
    curl -i -XPOST 'http://localhost:8086/write?db=demo' --data-binary 'server-load-average,host=server02,region=us-west value=0.077 1490166986000000000'

在grafana创建一个连向influxdb的数据源,Type选influxdb，Url填http://influx:8086 Database填demo


在grafana创建一个dashboard

1. 点击**ADD ROW**,创建一个Graph,点击标题进入编辑模式，点击edit开始编辑图表

2. 进入Metrics标签页，点击Add query增加一个查询,在A和B查询填入以下内容

    SELECT mean("value") FROM "server-load-average" WHERE "host" = 'server01' AND $timeFilter GROUP BY time(1m) fill(null)
    SELECT mean("value") FROM "server-load-average" WHERE "host" = 'server02' AND $timeFilter GROUP BY time(1m) fill(null)

3. 编辑完成回到主页面，点击右上角的时间信息,在Time Range中选择From:2017-03-22 15:05:00 to: 2017-03-22 15:16:45 点击Apply


### influxdb收集日志

我们可以用http请求来向influxdb写入一条数据,象下面这条请求这样

    curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'

这时influxdb就会在mydb数据库中自动创建一个cpu_load_short的表，以host,region作为索引字段 value作为普通字段 2015-06-11T20:46:02为时间字段


下面就是插入格式是的格式， 其中measurement相当于表名. tag,field分表表示**索引字段**和**普通字段**  时间戳必须是以整数形式表示

    <measurement>[,<tag-key>=<tag-value>...] <field-key>=<field-value>[,<field2-key>=<field2-value>...] [unix-nano-timestamp]

在插入数据时不存在的表,字段会自动创建，所以不用当心插入不存在的字段导致错误的问题(如果时间字段不存在就会用当前时间填充)


### influxdb的数据类型

key的类型只能是字符串(包货tag-key和field-key)


field的类型有4种,整数类型/浮点数类型/布尔类型/字符串类型

```
# integer value
cpu_value=1i

# float value
cpu_load value=1

cpu_load value=1.0

cpu_load value=1.2

# boolean value
error fatal=true

# string value
event msg="logged out"

```


### influxdb安全验证

1. 在influxdb配置中auth-enabled置为true

    vim influxdb/influxdb.conf

```
[http]
  enabled = true
  bind-address = ":8086"
  auth-enabled = true
  ...
```

2. 进入influx cli创建一个admin用户

```
./boot.sh exec influx influx
CREATE USER admin WITH PASSWORD 'mysecret' WITH ALL PRIVILEGES
```

3. 在custom-compose配置中修改influx的port配置，允许外部访问influx的8086接口

```
influx:
  ports:
    - 8086:8086
```

2. 删除旧的docker并重新生成所有服务(influx和grafana的数据都保存在volume里，所以不用当心被删掉)

    ./boot.sh stop && ./boot.sh rm -f && ./boot.sh up -d

5. 使用curl查看数据库信息

    curl -i -G http://localhost:8086/query -u admin:mysecret --data-urlencode "q=SHOW DATABASES"




### 备份

grafana的配置存放位置

    echo `pwd`/grafana

influxdb的数据存放位置

    sudo docker volume inspect -f "{{ .Mountpoint }}" monitor_influx-storage

### 关于

[influxdb文档](https://docs.influxdata.com/influxdb/v1.2)


[grafana文档](https://grafana.com/)


[docker一键安装](https://get.docker.com/)

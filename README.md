[![Travis Status for henson/MusicDownloader](https://travis-ci.org/henson/ProxyPool.svg?branch=master)](https://travis-ci.org/henson/ProxyPool) [![Go Report Card](https://goreportcard.com/badge/github.com/henson/ProxyPool)](https://goreportcard.com/report/github.com/henson/ProxyPool)

# Golang实现的IP代理池

> 采集免费的代理资源为爬虫提供有效的代理


### 1、代理池设计

　　代理池由四部分组成：

* Getter：

　　代理获取接口，目前有6个免费代理源，每调用一次就会抓取这个6个网站最新的100个代理放入Channel，可自行添加额外的代理获取接口；

* Channel：

　　临时存放采集来的代理，通过访问稳定的网站去验证代理的有效性，有效则并存入数据库；

* Schedule：

　　用定时的计划任务去检测数据库中代理IP的可用性，删除不可用的代理。同时也会主动通过Getter去获取最新代理；

* Api：

　　代理池的访问接口，提供get接口输出JSON，方便爬虫直接使用。

### 2、代码实现

* Api：

　　api接口相关代码，提供`get`接口，输出JSON；

* Storage：

　　数据库相关代码，数据库采用Mongo；

* Getter：

　　代理获取的相关代码，目前抓取：[快代理](http://www.kuaidaili.com)、[代理66](http://www.66ip.cn)、[IP181](http://www.ip181.com)、[有代理](http://www.youdaili.net/Daili/http/)、[西刺代理](http://www.xicidaili.com/nn/)、[guobanjia](http://www.goubanjia.com/free/gngn/index)这个六个网站的免费代理，经测试这些网站每天更新的可用代理只有六七十个，当然也支持自己扩展代理接口；

* Schedule：

　　定时任务，目前在main.go中以轮询方式实现，后期会改进；

* Util：

　　存放一些公共的模块、方法或函数，包含`Config`:读取配置文件config.json；

* 其他文件：

　　配置文件:config.json，数据库配置和代理获取接口配置；

```
{
    "mongo": {
        "addr": "mongodb://127.0.0.1:27017/",
        "db": "temp",
        "table": "pool",
        "event": "event"
    },
    "host": ":8080"
}
```

### 3、安装及使用

因为有些代理网站使用了加密页面、混淆代码等反爬技术，要正确采集到代理数据得用到 [PhantomJS](http://phantomjs.org/) ，必须提前先装好。

另外，本项目用到的依赖库有：
```
gopkg.in/mgo.v2
gopkg.in/mgo.v2/bson
github.com/PuerkitoBio/goquery
github.com/nladuo/go-phantomjs-fetcher
```

下载本项目：
```
go get -u github.com/henson/ProxyPool
```

然后配置好相应的config.json并启动：
```
go build
./ProxyPool
```

访问如下地址：
```
GET http://localhost:8080/v1/ip
```
![HTTP](pics/http.png)

### 4、感谢

感谢 [J_hao104](https://github.com/jhao104/proxy_pool) 提供思路。
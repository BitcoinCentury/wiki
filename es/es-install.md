**Elasticsearch版本是 6.2.3**

#### what is elasticsearch

elasticsearch简称es
>Elasticsearch 是一个分布式的 RESTful 风格的搜索和数据分析引擎，能够解决不断涌现出的各种用例。
作为 Elastic Stack 的核心，它集中存储您的数据，帮助您发现意料之中以及意料之外的情况。

#### 安装

首先安装java
`sudo apt-get install java`

下载 [es-deb](https://www.elastic.co/downloads/elasticsearch)
安装 `sudo dpkg -i elasticsearch-6.2.3.deb`
程序位置 `/usr/share/elasticsearch/`
配置文件 `/etc/elasticsearch/elasticsearch.yml`

#### 启动es

二选其一

`service elasticsearch start`

`/usr/share/elasticsearch/bin/elasticsearch -d` 作为一个守护进程在后台运行

是否运行

```
curl http://localhost:9200/

{
  "name": "n93oTbA",
  "cluster_name": "elasticsearch",
  "cluster_uuid": "PccwXXJx0d4C21ZQvDw",
  "version": {
    "number": "6.2.3",
    "build_hash": "cd9ff00",
    "build_date": "2018-03-13T10:06:29.741383Z",
    "build_snapshot": false,
    "lucene_version": "7.2.1",
    "minimum_wire_compatibility_version": "5.6.0",
    "minimum_index_compatibility_version": "5.0.0"
  },
  "tagline": "You Know, for Search"
}
```

##### 停止es

```
cat /var/run/elasticsearch/elasticsearch.pid  && echo # 获得pid

sudo kill -SIGTERM 15516
```

#### Kibana
> Kibana 让您能够可视化es中的数据并操作es
![kibana.jpg](https://upload-images.jianshu.io/upload_images/47789-a6df3fc24d75d190.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载[kibana-deb](https://www.elastic.co/cn/downloads/kibana)
安装 `sudo dpkg -i kibana-6.2.4-amd64.deb`
位置 `/usr/share/kibana/`
启动 `/usr/share/kibana/bin/kibana`
访问 `http://localhost:5601`

#### 手动安装Kibana

手动安装方便自定义配置

```
git clone https://github.com/elastic/kibana.git
cd kibana
git checkout v6.2.3 #es是6.2.3，kibana就切换到 v6.2.3
```

去掉Kibana对nodejs版本的限制
为啥 听人劝 吃饱饭

```diff
#package.json文件

   "engines": {
-    "node": "6.12.2",
     "yarn": "^1.3.2"
   }
 }
```

安装yarn `npm install -g yarn`
安装kibana的依赖 `yarn kbn bootstrap`
启动kibana `yarn start`

修改配置文件

```diff
# config/kibana.yml

-server.host: "localhost"
+server.host: "192.168.1.2"

-elasticsearch.url: "http://localhost:9200"
+elasticsearch.url: "http://192.168.1.1:9200/"
```

#### Kibana console

>![Sense.png](https://upload-images.jianshu.io/upload_images/47789-800923b0904d0a6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请求 es api `http://localhost:5601/app/kibana#/dev_tools/console?_g=()`

---

参考:

https://github.com/elastic/kibana/blob/master/CONTRIBUTING.md#setting-up-your-development-environment 

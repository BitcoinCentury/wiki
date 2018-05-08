#### 本人的 Elasticsearch 版本是 6.2.3

#### 下载并安装插件
```
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.3/elasticsearch-analysis-ik-6.2.3.zip
# sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install http://localhost:8000/elasticsearch-analysis-ik-6.2.3.zip -v
```

插件安装在 `/usr/share/elasticsearch/plugins/`
插件的配置文件在 `/etc/elasticsearch/analysis-ik`
测试下 `sudo ./bin/elasticsearch-plugin list`

**注意文件的权限**

### 当然也可以自己编译

#### 安装java Maven
[Maven](https://maven.apache.org/)  是一个项目管理和构建自动化工具, 类似 python的pipenv, nodejs的npm

`sudo apt-get install maven`

#### 编译 elasticsearch-ik

```
https://github.com/medcl/elasticsearch-analysis-ik.git
git checkout v6.2.3
mvn package
```

#### 重启 elasticsearch

`service elasticsearch restart`

#### 设置中文索引
```
curl -X PUT http://localhost:9200/weibo #新建一个index

curl -XPUT http://localhost:9200/weibo/_mapping/news -H 'Content-Type: application/json' -d'
{
  "properties": {
    "summary": {
      "type": "text",
      "analyzer": "ik_smart"
    },
    "title": {
      "type": "text",
      "analyzer": "ik_smart"
    }
  }
}'
```

> ik_max_word: 会将文本做最细粒度的拆分,比如会将 "中华人民共和国国歌"拆分为
"中华人民共和国,中华人民,中华,华人,人民共和国,人民,人,民,共和国,共和,和,国国,国歌"，会穷尽各种可能的组合
ik_smart: 会做最粗粒度的拆分, 比如会将 "中华人民共和国国歌"拆分为 "中华人民共和国,国歌" .

---

参考:

https://stackoverflow.com/questions/15630055/how-to-install-maven-3-on-ubuntu-17-04-16-10-16-04-lts-15-10-15-04-14-10-14-04-l
https://github.com/medcl/elasticsearch-analysis-ik
http://www.cnblogs.com/xing901022/p/5910139.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html
http://www.cnblogs.com/softidea/p/6081326.html

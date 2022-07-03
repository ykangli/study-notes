# Elasticsearch手动启动

1. Run `bin/elasticsearch` (or `bin\elasticsearch.bat` on Windows) to [start Elasticsearch with security enabled](https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-stack-security.html).
2. ![image-20220630101144141](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220630101146084.png)

# ElasticSearch 配置服务开机运行

**进入命令行切到 ElasticSearch 安装目录下的 bin 文件夹，并执行：**

```bash
elasticsearch-service.bat install
```

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-f5b49c6c3953216305cb13ff9e586791_1440w.jpg)

(如果删除 ElasticSearch 服务替换 install 为 remove 执行)

**执行命令 elasticsearch-service.bat manager**

执行后会弹出对话框，将启动类型 Startup type 改为 “Automatic" 自动

点击 Start 按钮启动服务，启动后点击确定即可

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-2b0a8648304bcf147a35c403bc9af3e4_1440w.jpg)

**查看是否成功**

win+R 运行 services.msc

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-b4884ae071914320dd721c606e774c37_1440w.jpg)

ElasticSearch 已经加到服务并且状态为“正在运行”

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-ff0ea1735c9fc0adad35a995f5abbe83_1440w.jpg)

以后每次开机时 ElasticSearch 都会以服务形式自动启动

# Kibana启动

1. Run `bin/kibana` (or `bin\kibana.bat` on Windows)
2. Click on the link provided in the terminal, or point your browser at [http://localhost:5601](http://localhost:5601/) and follow [enrollment instructions](https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-stack-security.html) to connect to Elasticsearch


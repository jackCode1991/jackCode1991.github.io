---
title: ES看这一篇就够了
date: 2021-07-30 16:57:02
category: 架构设计
tags:
  - 组件
  - 分布式
sticky: 1
---

##### Elasticsearch插件安装

1. 下载插件
2. 启动

```bash
#启动es服务 bin目录下的elasticsearch.bat文件，双击即可启动
#启动可视化界面
npm install
npm run start
```

3. 链接测试发现，存在跨域问题，配置es

```bash
http.cors.enabled: true
http.cors.allow-origin: "*"
```

4. 重启es

![image](/assets/cover/elsticSearch-1.png)

初学可以当作一个数据库！(可以建立索引(库))

> 这个head我们就把它当作数据展示工具！查询在kibana

1.安装kibana

2.汉化

![image](/assets/cover/elsticSearch-2.png)

ik分词

![image](/assets/cover/elsticSearch-3.png)

> 安装

1.下载插件

2.放入到elasticsearch插件即可

![image](/assets/cover/elsticSearch-4.png)

3.重启观察ES，可以看到分词器被加载

![image](/assets/cover/elsticSearch-5.png)

4.elasticsearch-plugin可以通过这个命令来查看加载进来的插件

![image](/assets/cover/elsticSearch-6.png)

5.使用kibana测试！

> 查看不同的分词效果

其中ik_smart为最少切分

![image](/assets/cover/elsticSearch-7.png)

ik_max_word最细力度切分

![image](/assets/cover/elsticSearch-8.png)

> 如何配置自己的字典

![image](/assets/cover/elsticSearch-9.png)

##### Rest风格说明

![image](/assets/cover/elsticSearch-10.png)

> 基础测试

1、创建一个索引

```bash
PUT /索引名/~类型名~/文档id
{请求体}
```

![image](/assets/cover/elsticSearch-11.png)

2、字段类型![image](/assets/cover/elsticSearch-12.png)

3.指定字段的类型

![image](/assets/cover/elsticSearch-13.png)

4.获取具体的规则信息

![image](/assets/cover/elsticSearch-14.png)

5.查看默认的类型

![image](/assets/cover/elsticSearch-15.png)

如果自己的文档字段没有指定，那么es就会给我们默认指定！

扩展：通过命令elasticsearch索引情况！

![image](/assets/cover/elsticSearch-16.png)

> 修改索引

![image](/assets/cover/elsticSearch-17.png)

> 删除

通过deete命令实现删除、根据你的请求类判断删除索引还是删除文档

##### 关于文档的基本操作（重点）

> 基本操作

1.添加数据

```java
PUT /kuangshen/user/1
{
  "name":"狂神说",
  "age":3,
  "desc":"一顿操作猛如虎，以看工资2500",
  "tags":["技术宅","温暖","直男"]
}
```

![image](/assets/cover/elsticSearch-18.png)

2.获取数据

![image](/assets/cover/elsticSearch-19.png)

3、更新数据 PUT（这样更新必须把所有字段列出，否则变空）

![image](/assets/cover/elsticSearch-20.png)

4、Post    _update,推荐使用这种方式

![image](/assets/cover/elsticSearch-21.png)

**简单搜索**

```bash
GET kuangshen/user/1
```

简单的条件查询

![image](/assets/cover/elsticSearch-22.png)

> 复杂操作 select(排序、分页、高亮、模糊查询)

![image](/assets/cover/elsticSearch-23.png)

输出结果，不想要那么多！

![image](/assets/cover/elsticSearch-24.png)

> 排序！

![image](/assets/cover/elsticSearch-25.png)

> 分页

```java
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神"
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ],
  "from": 0,
  "size": 2
}
```

> 布尔值查询

![image](/assets/cover/elsticSearch-26.png)

```java
GET kuangshen/user/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "狂神"
          }
        }
      ]
    }
  }
}
```



must （and）

should（or）

must_not (not)

> 过滤查询

```java
GET kuangshen/user/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "狂神"
          }
        }
      ],
      "filter": {
        "range": {
          "age": {
            "gt": 10
          }
        }
      }
    }
  }
}
```

![image](/assets/cover/elsticSearch-27.png)

> 匹配多个条件！

![image](/assets/cover/elsticSearch-28.png)

> 精确查询

term查询是直接通过倒排索引指定的词条进行精确的查找

**关于分词**：text    keyword

+ term，直接查询精确的

+ match，会使用分词器解析！

两个类型

![image](/assets/cover/elsticSearch-29.png)

![image](/assets/cover/elsticSearch-30.png)

![image](/assets/cover/elsticSearch-31.png)

> 精确查询多个值

![image](/assets/cover/elsticSearch-32.png)

> 高亮查询！

```bash
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神说"
    }
  },
  "highlight": {
    "pre_tags": "<p class='key' style='color:red'> ", 
    "post_tags": "</p>", 
    "fields": {
      "name":{}
    }
  }
}
```

![image](/assets/cover/elsticSearch-33.png)

##### 集成springboot

> 找官方文档

1.找原生的文档

~~~xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.6.2</version>
</dependency>
~~~

2.找对象

3.分析类中的方法
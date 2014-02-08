声明
========

本人自知精力与能力有限，欢迎志同道合之士送上您宝贵的建议与Patch!


关于
========

一个分布式定向抓取集群的简单实现。

Reference:
    程序架构参考淘宝官博：www.searchtb.com/2011/07/快速构建实时抓取集群.html

    目前实现功能
    -------------

    1. 多线程下载,线程数可配置。
    2. 无需修改代码，按照规则添加配置就可以完成页面抽取、入库。
    3. 利用Redis的list作为抓取队列，zset作为已抓取集合。
    4. 支持分布式部署多个爬虫，Redis作为核心，mysql为存储,当然redis/mysql自身拥有各自的扩展方案。


    TODO List
    -------------

    1. KISS: Keep it simple & stupid!
    2. Supports cookies,and authentication.
    3. Write information into files (using protobuf??).


# ElasticSearch学习笔记

[TOC]



# 1.简介

ElasticSearch是一个分布式开源的全文检索工具

Elasticsearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java语言开发的，并作为Apache许可条款下的开放源码发布，是一种流行的企业级搜索引擎。Elasticsearch用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。官方客户端在Java、.NET（C#）、PHP、Python、Apache Groovy、Ruby和许多其他语言中都是可用的。根据DB-Engines的排名显示，Elasticsearch是最受欢迎的企业搜索引擎，其次是Apache Solr，也是基于Lucene。

docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -v /home/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml  -v /home/elasticsearch/dir:/usr/share/elasticsearch/dir -e "discovery.type=single-node"    elasticsearch:7.6.1   




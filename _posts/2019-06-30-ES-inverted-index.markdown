---
layout: post
title: Elasticsearch 实战（05）倒排索引
date: 2019-06-30 14:02:11.000000000 +09:00
tags: 倒排索引 全文搜索
---

## 概要

> Elasticsearch 使用一种称为 倒排索引 的结构，它适用于快速的全文搜索。一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表。

比如，对于搜索引擎而言，正排索引就是文档 id 到单词的关联，而倒排索引就是单词到文档 id 的关联。

可以为 ES 的文档字段指定哪些不进行索引，这样做可以节省空间，但也意味着这个字段无法被搜索。

## 核心组成

![](http://ww1.sinaimg.cn/large/006tNc79ly1g4j674gktkj30n9067dfv.jpg)

倒排索引包含两部分：

* 单词词典 （ Term Dictionary ）：记录单词以及单词到倒排列表的关系，一般采用 B+ 树或者哈希拉链法实现

* 倒排列表 （ Posting List ）：记录单词对应文档集合，由倒排索引项 （ Posting ） 组成 —— 文档 ID、词频 TF、位置 Position、位移 offset

![](http://ww3.sinaimg.cn/large/006tNc79ly1g4j6bh8wsbj30n807vdfv.jpg)

## 参考资料和相关阅读

* Elasticsearch核心技术与实战 （极客时间 阮一鸣） [https://time.geekbang.org/course/intro/197](https://time.geekbang.org/course/intro/197)

* 维基百科 - 倒排索引 [https://zh.wikipedia.org/wiki/%E5%80%92%E6%8E%92%E7%B4%A2%E5%BC%95](https://zh.wikipedia.org/wiki/%E5%80%92%E6%8E%92%E7%B4%A2%E5%BC%95)

* Elasticsearch: 权威指南 - 倒排索引 [https://www.elastic.co/guide/cn/elasticsearch/guide/current/inverted-index.html](https://www.elastic.co/guide/cn/elasticsearch/guide/current/inverted-index.html)
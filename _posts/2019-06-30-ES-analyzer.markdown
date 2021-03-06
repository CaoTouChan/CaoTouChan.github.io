---
layout: post
title: Elasticsearch 实战（06）分词器 Analyzer
date: 2019-06-30 14:30:11.000000000 +09:00
---

## 概要

分词器，就是讲文本转换成一系列单词的工具。

分词器由三部分组成：

* Character Filters ：针对原始文本，去除 HTML

* Tokenizer ： 按照规则切分单词

* Token Filters ： 去除停用词、大小写改动之类

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/006tNc79ly1g4j6mqzjqzj30l706mdfv.jpg)


## 分类

ES 有内置的分词器，也可以自行定制分词器。其内置了多种分词器，比如 ：

* Simple Analyzer – 按照非字母切分（符号被过滤），小写处理
* Stop Analyzer – 小写处理，停用词过滤（the，a，is）
* Whitespace Analyzer – 按照空格切分，不转小写
* Keyword Analyzer – 不分词，直接将输入当作输出
* Patter Analyzer – 正则表达式，默认 \W+ (非字符分隔)
* Language – 提供了30多种常见语言的分词器

示例代码如下：

```python
//查看不同的analyzer的效果
//standard
GET _analyze
{
  "analyzer": "standard",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}

//simpe
GET _analyze
{
  "analyzer": "simple",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}


GET _analyze
{
  "analyzer": "stop",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}


//stop
GET _analyze
{
  "analyzer": "whitespace",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}

//keyword
GET _analyze
{
  "analyzer": "keyword",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}

GET _analyze
{
  "analyzer": "pattern",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}


//english
GET _analyze
{
  "analyzer": "english",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}


POST _analyze
{
  "analyzer": "icu_analyzer",
  "text": "他说的确实在理”"
}


POST _analyze
{
  "analyzer": "standard",
  "text": "他说的确实在理”"
}


POST _analyze
{
  "analyzer": "icu_analyzer",
  "text": "这个苹果不大好吃"
}
```


## 参考资料和相关阅读

* Elasticsearch核心技术与实战 （极客时间 阮一鸣） [https://time.geekbang.org/course/intro/197](https://time.geekbang.org/course/intro/197)

* Anatomy of an analyzer [https://www.elastic.co/guide/en/elasticsearch/reference/current/analyzer-anatomy.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/analyzer-anatomy.html)


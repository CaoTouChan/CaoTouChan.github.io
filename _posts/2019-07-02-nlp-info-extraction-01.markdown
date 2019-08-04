---
layout: post
title: 信息抽取概述
date: 2019-07-02 09:02:11.000000000 +09:00
tags: 信息抽取 命名实体识别 
---

## 概述

信息抽取 （ Information Extraction, IE ） 是把文本中的非结构化信息转化为结构化信息的过程。

大部分 IE 任务中开始于 **命名实体识别 （ NER ）** 。这个任务是从文本中找到每个命名实体并标记其类型。常见的命名实体有地点、人物、组织等。NER 对于共指消歧 （ coreference resolution ） 和实体链接 （ entity linking ）都很重要。

然后，本文将会描述 **关系抽取** 这个任务：找到并分类文本实体之间的语义关系。

第三部分将会讨论和事件有关的三个任务：**事件抽取 （ Event extraction ）、事件共指 （ Event coreference ）、时间表达 （ Temporal Expression ）**。

许多文章会描述一些反复发生的事件之类，所以文章的最后将会讨论 **模板填充 （ template filling is ）** 。

## 命名实体识别（ Named Entity Recognition, NER ）

| 类型        | 标签   |  具体分类  |  例子  |
| :-------:   | :-----:  | :----:  | :----:  |
| 人物     | PER |   人物、角色等     |  **鲁迅** 是个好人   |
| 组织     | ORG |   公司、俱乐部等     |  **曼联** 昨晚赢了   |
| 地点     | LOC |   山、海等     |  **喜马拉雅山** 也太高了   |
| 地缘政治实体     | GPE |   国家、省份等     |  我爱 **广州**    |
| 设施     | FAC |   高楼、桥、机场等     |  这就是 **金门大桥** 啊   |
| 交通工具     | VEH |   火车、飞机等     |  这是一款经典的 **福特猎鹰** |

上表描述了一些经典的命名实体类别。

标准的 NER 任务是将它视为一项序列标记任务：对每一个 word 输出一个标记。常用的标记方式有 IOB tagging ，比如：

> 川	B-PER 
> 
> 普	I-PER 
> 
> 爱	O 
> 
> 北	B-LOC 
> 
> 京	I-LOC 
> 
> 天	B-LOC 
> 
> 安	I-LOC 
> 
> 门	I-LOC

一般来说，会训练一个序列分类器来完成这项任务，常用 **MEMM/CRF**、 **bi-LSTM**、 或者**混合**使用。

评估指标一般使用：召回率、准确率和 F1 measure。

### Feature-based 算法

常规的特征有：

* identity of wi, identity of neighboring words 
* embeddings for wi, embeddings for neighboring words 
* part of speech of wi, part of speech of neighboring words 
* base-phrase syntactic chunk label of wi and neighboring words 
* presence of wi in a **gazetteer** 
* wi contains a particular prefix (from all prefixes of length ≤ 4) 
* wi contains a particular suffix (from all suffixes of length ≤ 4) 
* wi is all upper case 
* word shape of wi, word shape of neighboring words 
* short word shape of wi, short word shape of neighboring words 
* presence of hyphen

以下是利用 POS、Chunks 和 Word Shape 来训练分类器的例子：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/7M3mdCn6Zc9l6JQ7q9IH0.png)

### 神经网络算法

神经网络的算法通常是利用 bi-LSTM 来搭建模型，如下：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/AP4fX1feDavpMx6zoYyGQP1sy0e5s0r.png)

### Rule-based 算法

虽然学术界对 NER 玩出花了，但是商业中大部分依然使用的是 Rule-based 的算法，一般的步骤是：

1. 使用高精确率的规则标记无歧义实体
2. 搜索匹配上边名称的子串
3. 搜索特定领域实体列表以确定候选实体
4. 将前阶段的标记作为附加特征应用在概率序列标记技术中

## 关系抽取 （ Relation Extraction ）

关系抽取实际上就是从自然语言文本中抽取出三元组，比如：

* (founders, SpaceX, Elon_Musk)
* (has_spouse, Elon_Musk, Talulah_Riley)
* (worked_at, Elon_Musk, Tesla_Motors)

下文将简述关系抽取的方法，详细参见 [笔记 - CS224U NLU 07 关系抽取](http://caotouchan.tech/2019/07/cs224u-07-re/)。

### Pattern-based 方法

可以基于触发词/字符串/依存句法来进行抽取：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/2p6Re3lBGWpFso39WRJrciw3qvJtMBY.png)

### 监督学习方法

一些常用的监督学习方法使用的特征：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/WDgQpOzK4REWtVWKpBTiwI401kKHmnO.png)

### 半监督学习方法 （ via Boostrapping ）

使用 Boostrapping 的方法进行训练的流程如下：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/HQ6A8Qf2ORqHLuRNYEa09Fl4NNfb95S.png)

为了 “避免” 语义漂移（ semantic drift ），Boostrapping 系统给每个 tuple 赋予置信度（ confidence values ）。 

这个置信度平衡两个因素：

1. 模式相对于当前 tuple 集的性能
2. 模式在文档集合中生成的匹配数量方面的生产率

具体来说，对于给定的文档 $$\mathscr{D}$$、现有 tuple 集合 $$T$$、新发现的 pattern $$p$$，那么我们需要两个因子：

1. $$hints$$ ： the set of tuples in $$T$$ that $$p$$ matches while looking in $$\mathscr{D}$$
2. $$finds$$ ： the total set of tuples that $$p$$ finds in $$\mathscr{D}$$

下式权衡了上述两种因子 (Riloff and Jones, 1999) ：

$$Conf_{R l o g F}(p)=\frac{h i t s_{p}}{finds_{p}} \times \log \left(finds_{p}\right)$$

在 Boostrapping 过程中设置阈值有助于防止模型从目标关系上漂移。

### 远程监督学习方法

远程监督学习结合 Boostrapping 和监督学习，其从大型数据库中获取大量样本，创建大量噪声模式特征，然后将它们组合在有监督的分类器中，而不仅仅是少数种子。其基本流程如下：

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/kFqpGyolUogcDRiqmJZqNp7JnEqmwbO.png)

### 无监督学习方法

### 评估关系抽取

## 抽取时间

### Temporal Expression Extraction

### Temporal Normalization

## 抽取事件及其时间

### 时序问题

## 模板填充 （ Template Filling ）

### 机器学习方法

### 早期的有限状态 （ Finite-State ） 模板填充方法

## 参考资料

* Speech and Language Processing (3rd ed. draft) by Jurafsky and Martin, 2018, §17 [https://web.stanford.edu/~jurafsky/slp3/](https://web.stanford.edu/~jurafsky/slp3/)
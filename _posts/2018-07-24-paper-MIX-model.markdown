---
layout:       post
title:        "Multi-Channel Information Crossing for Text Matching"
subtitle:     "阅读笔记"
date:         2018-07-24 20:00:00
author:       "田鑫"
header-img:   "img/in-post/post-eleme-pwa/eleme-at-io.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - NLP
    - Text matching
    - Question answering
    - Attention mechanism
---

2018-07-26晚上8点浩蓝受将门邀请分享他们在text matching最新的研究进展，浩蓝在将门nlp技术社群分享了这篇论文，于是就有了这篇论文的阅读笔记，以供交流反思。

### 前导知识
短文本匹配有2大类解决方案，分别为:Presentation based Method和Interaction based Methods

###### Presentation based Methods

这类方法的思路都是用各种CNN、RNN等模型先去生成文本的特征表示(feature vector), 然后通过计算2个特征向量的相似度来度量文本的匹配度，这一类方法的区别就在于如何去计算文本的特征表示以及如何去度量特征向量的相似度。

###### Interaction based Methods

**基本假设** :两个句子的全局匹配度是由局部匹配度经过融合得到的;  
**主体思想** :先计算两个句子的局部匹配度，然后再去学习合适函数关系将局部匹配度融合起来作为最终的匹配度;  
**实现思路** :这类方法的思路是先去求文本对序列对的匹配矩阵，然后利用各种模型从匹配矩阵中学习出有意义的特征，进行最终的相似度计算;  
**优点** :(相对于Presentation based Methods)
1. 更直接的表示局部匹配的关系
2. 保留了更多的结构信息(匹配的位置信息)；

**缺点** :
1. 将word或者n-gram word作为特征的基本单元，没有考虑到自然语言的其它特征:例如句子的句法信息；
2. 很难建模global matching和local matching的关系;   
3. 对于文本多角度的匹配特征缺乏统一的融合框架，使得增加新的匹配特征简单便捷；

### Mix Model
**主体思路**
1. 多粒度抽取文本表示特征, 并交叉计算相似度矩阵，尽可能全面的捕捉基础信息；
2. 加入attention机制，对局部匹配的语义相似度进行更精细的刻画；
3. 利用卷积对多通道的local matching矩阵进行处理，从而刻画全局匹配度；

###### Mix Model想要解决什么问题？
再来具体化以前Interaction based Methods的缺点:

1. **仅仅从word embedding特征去计算local matching是有明显缺陷的, 对于短语来说会有明显的信息损失**
(例如: all in与in all, work hard与hard work, 这两组文本用word embedding计算出的相似度矩阵完全一致，但是两组文本的匹配程度却差异很大)
2. 假设局部匹配的特征重要度相同，实际情况并不是这样，例如:两个句子中的关键词匹配特征明显比两个句子中常用词的匹配度特征更重要；
3. 对于多角度特征匹配缺乏统一的集成机制, 如果想增加新的匹配特征很难。

**Mix model如何解决上述问题**
1. 对句子进行多粒度切分(体现在卷积核size的选择为1,2,3)，多粒度交叉计算local matching，尽可能少的信息损失;  
2. 通过引入attention mechanism对局部匹配特征对于全局匹配度的重要性加以描述； 
3. 设计统一的集成机制，让多角度匹配特征融合很容易；

###### Local Matching and global matching
核心思想:
* 将文本切分成不同粒度的sequence，通过设置不同卷积核大小学习文本不同粒度的表示，根据文本表示计算相似度矩阵;
* attention机制: 利用idf attention、pos attention、 position attention分别对原始的相似度矩阵加权;
* 将上述多通道的相似度矩阵融合起来最为最终的特征;

### 疑问与思考
1. 多粒度抽取文本特征的具体如何实现?  
答: 文章中通过不同size的卷积核对word embedding进行卷积就相当于对文本进行了序列切分；  

2. 文本卷积的结果在论文图中看起来是二维矩阵？  
答: 每个size的卷积核有N个, 假如文本长度为d，size=1，则卷积后的文本表示为N*d矩阵，表示文本1-gram的特征；

3. 相似度矩阵是如何求出来的；   
答: 向量内积；

4. 如果对文本划分序列的话，相应的attention矩阵如何计算？  
* bi-gram和tri-gram的词组作为单个单词去计算idf并学习相应的embedding, idf只是用来进行更为合理的初始化；
* POS和position同理，其中POS attention矩阵可以很方便地引入先验知识；


5. tri-gram特征爆炸如何处理?   
答: 通过hash function哈希到固定的bucket, 或者只取头部高频tri-gram, 低频直接丢弃;
如果通过hash函数映射的话，必然会有部分词组会碰撞，对有碰撞词组的学习到的特征表示精度损失为什么可以接受?

6. 向量空间如何实现快速索引?  
7. 短文本匹配和长文本匹配有什么区别?  

# 中文分词

## 1. 中文分词 <a id="&#x4E2D;&#x6587;&#x5206;&#x8BCD;"></a>

**分词技术**就是搜索引擎针对用户提交查询的关键词串进行的查询处理后根据用户的关键词串用各种匹配方法进行分词的一种技术。

**中文分词**（Chinese Word Segmentation）指的是将一个汉字序列（句子）切分成一个一个的单独的词，分词就是将连续的字序列按照一定的规则重新组合成词序列的过程。

现在分词方法大致有三种：基于字符串配置的分词方法、基于理解的分词方法和基于统计的分词方法。

今天为大家分享一个国内使用人数最多的中文分词工具GoJieba。

源代码地址：[http://www.github.com/yanyiwu/gojieba](http://www.github.com/yanyiwu/gojieba)

官方文档：[http://www.github.com/yanyiwu/gojieba/wiki](http://www.github.com/yanyiwu/gojieba/wiki)

### 1.1.1. 官方介绍 <a id="&#x5B98;&#x65B9;&#x4ECB;&#x7ECD;"></a>

支持多种分词方式，包括: 最大概率模式, HMM新词发现模式, 搜索引擎模式, 全模式

* 核心算法底层由C++实现，性能高效。
* 无缝集成到 Bleve 到进行搜索引擎的中文分词功能。
* 字典路径可配置，NewJieba\(...string\), NewExtractor\(...string\) 可变形参，当参数为空时使用默认词典\(推荐方式\)

### 1.1.2. 模式扩展 <a id="&#x6A21;&#x5F0F;&#x6269;&#x5C55;"></a>

* 精确模式：将句子精确切开，适合文本字符分析
* 全模式：把短语中所有的可以组成词语的部分扫描出来，速度非常快，会有歧义
* 搜索引擎模式：精确模式基础上，对长词再次切分，提升引擎召回率，适用于搜索引擎分词

### 1.1.3. 主要算法 <a id="&#x4E3B;&#x8981;&#x7B97;&#x6CD5;"></a>

* 前缀词典实现高效的词图扫描，生成句子中汉字所有可能出现成词情况所构成的有向无环图（DAG）
* 采用动态规划查找最大概率路径，找出基于词频最大切分组合
* 对于未登录词，采用汉字成词能力的HMM模型，采用Viterbi算法计算
* 基于Viterbi算法做词性标注
* 基于TF-IDF和TextRank模型抽取关键词

### 1.1.4. 编码实现 <a id="&#x7F16;&#x7801;&#x5B9E;&#x73B0;"></a>

```text
package main

import (
    "fmt"
    "strings"

    "github.com/yanyiwu/gojieba"
)

func main() {

    var seg = gojieba.NewJieba()
    defer seg.Free()
    var useHmm = true
    var separator = "|"

    var resWords []string
    var sentence = "万里长城万里长"

    resWords = seg.CutAll(sentence)
    fmt.Printf("%s\t全模式：%s \n", sentence, strings.Join(resWords, separator))

    resWords = seg.Cut(sentence, useHmm)
    fmt.Printf("%s\t精确模式：%s \n", sentence, strings.Join(resWords, separator))

    var addWord = "万里长"
    seg.AddWord(addWord)
    fmt.Printf("添加新词：%s\n", addWord)

    resWords = seg.Cut(sentence, useHmm)
    fmt.Printf("%s\t精确模式：%s \n", sentence, strings.Join(resWords, separator))

    sentence = "北京鲜花速递"
    resWords = seg.Cut(sentence, useHmm)
    fmt.Printf("%s\t新词识别：%s \n", sentence, strings.Join(resWords, separator))

    sentence = "北京鲜花速递"
    resWords = seg.CutForSearch(sentence, useHmm)
    fmt.Println(sentence, "\t搜索引擎模式：", strings.Join(resWords, separator))

    sentence = "北京市朝阳公园"
    resWords = seg.Tag(sentence)
    fmt.Println(sentence, "\t词性标注：", strings.Join(resWords, separator))

    sentence = "鲁迅先生"
    resWords = seg.CutForSearch(sentence, !useHmm)
    fmt.Println(sentence, "\t搜索引擎模式：", strings.Join(resWords, separator))

    words := seg.Tokenize(sentence, gojieba.SearchMode, !useHmm)
    fmt.Println(sentence, "\tTokenize Search Mode 搜索引擎模式：", words)

    words = seg.Tokenize(sentence, gojieba.DefaultMode, !useHmm)
    fmt.Println(sentence, "\tTokenize Default Mode搜索引擎模式：", words)

    word2 := seg.ExtractWithWeight(sentence, 5)
    fmt.Println(sentence, "\tExtract：", word2)

    return
}
```

运行结果

```text
    go build -o gojieba 

    time ./gojieba 

    万里长城万里长  全模式：万里|万里长城|里长|长城|万里|里长 
    万里长城万里长  精确模式：万里长城|万里|长 
    添加新词：万里长
    万里长城万里长  精确模式：万里长城|万里长 
    北京鲜花速递    新词识别：北京|鲜花|速递 
    北京鲜花速递    搜索引擎模式：北京|鲜花|速递
    北京市朝阳公园  词性标注：北京市/ns|朝阳/ns|公园/n
    鲁迅先生        搜索引擎模式：鲁迅|先生
    鲁迅先生        Tokenize Search Mode 搜索引擎模式：[{鲁迅 0 6} {先生 6 12}]
    鲁迅先生        Tokenize Default Mode搜索引擎模式：[{鲁迅 0 6} {先生 6 12}]
    鲁迅先生        Extract：[{鲁迅 8.20023407859} {先生 5.56404756434}]

    real    0m1.746s
    user    0m1.622s
    sys     0m0.124s
```

转自公众号：sir小龙虾


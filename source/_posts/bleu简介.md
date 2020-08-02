title: bleu简介
categories: 学习笔记
mathjax: true
date: 2020-07-28 20:56:49
tags:
---
&emsp;&emsp;BLEU (Bilingual Evaluation Understudy), 其意思是双语评估替补。该指标首次提出是用于对机器翻译结果的评估，但是后来越来越多地用于对各种自然语言处理任务的评估。在某些任务中，虽然bleu值不是一个很完美的评估指标，但是由于其计算简单且快速，不需要人工评估，所以对于这些任务还是能够提供一定指导意义的。本文只介绍bleu定义及nltk的使用。
<!--more-->
##### bleu的定义及计算方法
&emsp;&emsp;bleu指标背后的思想很简单，就是我们在评估两个句子是否相似的时候，一般会看两个句子中是不是采用了相同的字或者词，如果相同的字或者词越多的话，说明两个句子越相似，在翻译中就是翻译的质量越好。虽然这种方法忽略了相似词，但是仍旧有一定的指导意义。  
&emsp;&emsp;在进一步介绍bleu之前，需要了解两个概念，参考翻译和候选翻译集。在翻译中，对于一个文本，会通过人工翻译出来多个参考答案。候选翻译集就是模型翻译出来的结果。在计算bleu值，我们会将候选翻译集中句子依次和参考翻译中的句子进行比较，计算出对应的指标。了解这两个定义之后，下面先直接放出bleu的计算公式。
$$
BLEU = BP \cdot exp(\sum_{n=1}^N \omega_n \log(P_n))
$$   
$$
BP = 
\begin{cases}
1& if \quad c>r \\\\
e^{1-\frac{r}{c}}& if \quad c \leq r
\end{cases}
$$
&emsp;&emsp;其中BP是惩罚系数，会对较短的翻译句子进行惩罚；$N$是N-grams，一般$N$为4，表明我们会基于n-grams（n=1，2，3，4）来计算bleu值；$\omega_n$是各个grams的权重；$P_n$是计算各个grams下的重合度，具体计算方法后面会介绍。  
&emsp;&emsp;以上的计算方法是经过多次改进后的，我们下面介绍下各个改进的原因。
##### $P_n$的计算方法
&emsp;&emsp;考虑1-grams，假如我们有以下的候选句子和参考翻译：  
- 候选句子：the the the the the the the  
- 参考句子1：the cat is on the mat  
- 参考句子2：there is a cat on the mat  

&emsp;&emsp;在计算$P_1$的时候，由于the这个单词在参考句子中出现过，而且候选句子都是the，所以分子是7，分母是候选句子的长度，也是7，所以最后得分是1，这和我们的理解完全不一样，所以为了避免生成这些无意义的翻译，我们需要对单词重复出现的次数进行限制。  
&emsp;&emsp;还是以上面的例子为例，限定分子中重复单词的个数，有多种方法可以解决这个问题，下面介绍其中一种方法的计算流程。
- 统计候选句子和参考句子中the的出现次数，在候选句子中the出现了7次，参考句子1中出现了2次，参考句子2出现了1次。
- 将候选句子的7次和参考句子1的2次比较，选择较小的，即为2次；然后将候选句子的7次和参考句子2的1次比较，选择较小的，即为1次。
- 将两次比较结果的2次和1次比较，选择较大的，即为2次，则对于候选句子分子为2。
- 分母仍然是候选句子的长度7，则可得$P_1=\frac{2}{7}=0.286$。  

&emsp;&emsp;上面方法计算得到的就是1-grams的值，从这结果可以看出1-grams还是不能有效反映两个句子的相似性，这时候就得考虑引入n-grams了。
##### 多个n-grams的组合
&emsp;&emsp;按照上面的思路我们计算得到了1-grams，根据同样的思路可以计算2-grams(the, the)，3-grams(the, the, the)，4-grams(the, the, the, the)的分值，由于这些多元组在参考句子中均为出现，所以结果均为0。为了组合这几个分数，我们会取对数之后再进行加权，但这会有个问题就是0没有对数，所以在实际处理过程中会有各种方法进行修正。  
&emsp;&emsp;在使用nltk的时候，可以通过`SmoothingFunction`来选择各种修正方法，默认是`SmoothingFunction().method0`。在实际使用的时候，不同的修正方法对结果还是有很大影响的。但是只要采用同个修正方法，结果具有对比性就可以了。
```
from nltk.translate.bleu_score import sentence_bleu
from nltk.translate.bleu_score import SmoothingFunction

smoothie = SmoothingFunction().method4
sentence_bleu([["你", "好", "啊"]], ["你", "好"], smoothing_function=smoothie)
```
&emsp;&emsp;上面计算出来的bleu值比较依赖于我们采用的修正方法。为了解决这个问题，我们可以采用`corpus_bleu`，和`sentence_bleu`只计算单个句子的bleu值不同，`corpus_bleu`是计算整体的bleu值，这样可以降低某个grams缺失的影响。  
&emsp;&emsp;比如有两个候选句子A和B，原来句子A的4-grams的计算方法是$P_4=\frac{n_{4A}}{L_A}$，其中$n_{4A}$是和参考句子重合的4-grams个数，$L_A$是句子A的长度，同理句子B的4-grams是$P_4=\frac{n_{4B}}{L_B}$，如果没有重合的4-grams的话，计算出来的$P_4=0$。采用`corpus_bleu`的话，$P_4=\frac{n_{4A}+n_{4B}}{L_A+L_B}$，如果文档足够多的话，则可以避免为0了，也无需采用修正方法了。    
&emsp;&emsp;所以在使用bleu的时候，需要考虑是采用`sentence_bleu`还是`corpus_bleu`，如果关注某个句子的bleu值，需要对具体case进行分析的话，可以采用`sentence_bleu`，如果关注整体性能的话，可以采用`corpus_bleu`。
##### 译句较短惩罚
&emsp;&emsp;考虑下面的例子，按照上面方法计算出来的$P_1$和$P_2$都是1，所以为了解决这个问题，就对过短的句子增加了一个惩罚项。
- 候选句子：the cat 
- 参考句子1：the cat is on the mat  
- 参考句子2：there is a cat on the mat  

##### 参考
[机器翻译自动评估-BLEU算法详解](https://blog.csdn.net/qq_31584157/article/details/77709454)

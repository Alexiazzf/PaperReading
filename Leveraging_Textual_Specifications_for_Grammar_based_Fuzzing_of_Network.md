# 论文名称：Leveraging Textual Specifications for Grammar-based Fuzzing of Network
[TOC]

# 论文笔记

## 1. Abstract
Automated learning of protocol rules from textual specifications
(i.e. RFCs).从文本中自动学习协议规则
## 2. Introduction
### 背景：
Grammer Fuzzing 需要大量的人工操作并且难以transfer到其他protocol.

### Goals:
1. minimize the manual supervision effort required for training
2. adapt to new protocols without re-training.

### Contribution：
1. Define the problem of protocol grammar extraction as a set of NLP tasks. **提出思路**
2. Suggest and evaluate an NLP framework for these tasks, designed to reduce manual supervision effort when adapting to new network protocols. **设计模型**
3. Demonstrate the usefulness of the information extracted by our NLP framework by applying it to a transport protocol fuzzer. **模型评价**

## 3. Related Work

1. NLP的研究：句子识别，ontology创建 

缺点：a small, predefined set of entities; analyze small, structured sentences; and use rule-based approaches.
2. 协议规范：网络跟踪，程序分析，模型检测

缺点：need experts，bad for new protocol

## 4. Problem Defination

### Problem 1: Protocol Grammar-based Fuzzing

- Fuzzing


Fuzzing is a technique
used for finding software vulnerabilities by injecting
random inputs and then observing the output of the program
under test.

Fuzzing（模糊测试）技术是一种基于黑盒（或灰盒）的测试技术，通过自动化生成并执行大量的随机测试用例来发现产品或协议的未知漏

- Grammer Based Fuzzing：Fig. 2


### Problem 2: Protocol Grammar Extraction

```
graph LR
Protocol-->fields
Protocol-->properties
```

**Task 1 ：Type Extraction**, given a protocol document, extract the set of protocol field and property symbols.

**Task 2 ：Symbol Identification and Linking**, given the document
and the set of extracted symbols, ++identify mentions++ of these
symbols in text, and link together field mentions to their relevant
properties, as indicated by the protocol text.

### Problem 3: Zero Shot Learning for Entity and Property Linking
traditional fully supervised approach: T -> E

ZSL: <T,E> -> {t,f}     (<Input, Output> -> Bool)

*the system is expected to perform well even over outputs that were not observed during training*

similarity metric, sim(t, ei) and defining the prediction as:
argmaxsim(t, ei).

## 5. Design
Model ：
```
graph TD

    A[Raw Specification Document] --> B(Pre-processing) 

    B(Pre-processing) --> E(Field/entity mentions identification)
    D(List of entity type) -->E(Field/entity mentions identification)
    E(Field/entity mentions identification) --> F(Property Extraction by examining their context)
    G[Property]--> F(Property Extraction by examining their context)
    F(Property Extraction by examining their context) --> H(Post-processing)
    H(Post-processing) --> I[Cleaned properties with the packet fields]

```
### Entity Mention Identification

(ej,ci) --> True

(ej,ci) --> False --> similarity --> new entity type

by SVM


### Property Extraction

look for the same properties in each protocol (select 9 properties including checksum, port, multiple)

identifies chunks of text that express a property
to determine which property, choosing the property with the maximum key phrase overlap

choose the entity type defined in the title of the section in which the property appears. (Fig.4)


### Post-processing
leveraging domain specific knowledge

guess which fields have these properties based on field names and sizes

## 6. Evaluation 模型评测

### Information Extraction Evaluation 抽取准确率
- **Table 1**：

O：Overlap between an entity type and the current chunk.

RB：Rule-based systems based on our feature set.

There is value in both informative features and the use of our learning framework

- **Table 2**：

S-TPR (true positive rate at the span level)

C-FPR (rate of false chunks that our classifier miss-classifies as properties)

O： Identifying most properties is essential for the performance of the fuzzer, while we can live with some level of noise and rely on the postprocessing step.

RB:The level of noise introduced with these systems is too high.

Our approach gives us a better balance between the number of properties found and the level of noise introduced.

- **Table3**：

Total(K): The list of entity types is known a priori (K)

Total(E): When the list of entity types is extracted using the RFC format (E).

We only need to identify a single (property, entity) tuple, regardless of how many times it appears in the document

### Fuzzer Evaluation 模糊测试中的效果

运用了 **SNAKE Fuzzer**：The key component of SNAKE is a malicious proxy that
modifies and injects attack packets based on a protocol description
manually specified by an expert.

Fuzzer configurations 测试对象：

- Random
- Mannual
- NLP

Metrics 评价指标：
1. the amount of **effort** required to test an implementation 越少越好
2. the **coverage** of the generated tests 越高越好 **Table 4**
3. the overall **attack** discovery results 越高越好 **Table 5**




# 论文总结

## 1. 要解决什么问题

1. Minimize the manual supervision effort required for training
2. Adapt to new protocols without re-training

构建一个模型，从 raw specification document 中抽取 （entity，property）对

- **为什么这个问题这么重要？**

## 2. 如何解决的
提出了一个NLP-approch, 基于ZSL的方法，重要的步骤是

1. Entity Mention Identification
2. Property Extraction

- **为什么这个方法可以解决这个问题？**

## 3. 核心结论
1. 抽取准确率高
2. 模糊测试效果好（效率高，coverage高，attack discovery高）

- **下一步还可以怎么做？**

 
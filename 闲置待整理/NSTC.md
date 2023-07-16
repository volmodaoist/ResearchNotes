### 何为 NSTC?

**Neural Sequential Tensor Completion (NSTC)** is a technique for **inferring missing data** in multidimensional arrays (tensors) by exploiting their **spatiotemporal correlations**. It uses a neural network to model the interactions among data features and learn **high-order patterns from local to global ranges**. NSTC can be applied to various domains such as network monitoring, video completion, and recommender systems.

Some topics:

- Network Monitoring: NSTC can accurately recover the **performance metrics** of a large network by measuring only a subset of paths or time intervals and exploiting their spatiotemporal correlations
- Visual Data(e.g. Video Completion):NSTC can enhance the quality of images or videos **by filling in the missing pixels or frames** using their low-rank structure and high-order interactions3
- Recommender Systerms: NSTC can provide **personalized recommendations** for users by predicting their preferences or ratings for items based on their historical behaviors and similarities with other users



> 个人的一些疑问:
>
> - 是否类似AutoEncoder，通过自监督学习的方式掌握数据的某种表征呢？
> - 网络监管领域的 performance metrics 主要用来做什么呢？用来度量网络性能，通过历史数据训练模型，使其能够预测矩阵的缺失值？如果不故意挖空，为什么会有缺失值？
> - 张量补全主要目标是为了预测吗？这个思想是不是类似于NLP领域把句子挖空一部分再让模型做完形填空一样？矩阵挖空一部分再让模型预测？





###	为什么会有缺失值?

There are many possible causes of missing data in multidimensional arrays. Some of them are:

- **Data corruption**: 
  1. Data collection errors: Sometimes data may not be collected properly due to technical errors, system crashes, or connectivity issues. Not only **equipment malfunctions**, but also **human errors** are possible reason.
  2. Data processing errors: The collected data may undergo processing to remove outliers or to transform data to a more usable format. During this process, some data may be accidentally deleted or misplaced. (**Lost files**)

- **Intentional omission**: Data may be deliberately left out if it is deemed irrelevant or if it contains sensitive information, such as privacy concerns, avoiding negative perceptions, preventing spam or manipulation, or simply not relevant to business goals.
  - Some examples:
    - Website owners may intentionally omit tracking certain metrics such as bounce rate or time on page for certain pages that are not relevant to their business goals.
    - Social media platforms may intentionally omit certain metrics such as the number of times a post has been shared or the total number of followers of a user, in order to prevent spam or manipulation.
    - Companies may intentionally omit certain financial metrics such as debt-to-equity ratio or return on investment (ROI) to avoid negative perceptions from investors or stakeholders.

Missing data can affect the **quality and validity** of your analysis, so it is important to handle them properly. One way to do that is by using tensor completion methods such as NSTC (**This paragraph descibe the importance of NSTC**)

> 也就是说，由于种种原因导致了模型预测一个结果所需的全部特征往往是无法全部收集得到的，因而有必要通过 NSTC 进行预测。





Observed entries are the network monitoring data that are measured **by active probing of the network paths**. They are usually sparse and incomplete due to the high measurement cost and overhead. The missing entries can be recovered by using matrix completion techniques that leverage the **spatial-temporal** correlations among data. Observed entries are important for gaining a full knowledge of end-to-end network performance and reducing the measurement overhead. 



### LightNestle 分析

文章名称:

**Quick and Accurate Neural Sequential Tensor Completion via Meta Learning**

The paper evaluates the proposed scheme on real-world network data sets and shows that it **outperforms state-of-the-art methods** in terms of accuracy and efficiency. The paper was accepted by **IEEE INFOCOM** 2023, a CCF A-class international conference.

- The main contributions of this paper are:

  - It designs an expressive neural network to transfer spatial knowledge from previous embeddings to current embeddings.

  - It designs an attention-based module to transfer temporal patterns into current embeddings in linear complexity.

  - It applies meta-learning to quickly adapt to new network data with few-shot learning. 

- 文章结构分析
  - 文章的 Introduction 能够分成三个部分
    - Background & Motivation
    - Prior Arts and Limitations
      - Retraining on all data (全部训练一遍)
      - Retraining on new data (提到了sampling rate )
      - Retraining by fine-tuning (前面的参数拿来初始化)
    - Our Contributions
    - Problem Formulation (指出当前阶段的两难境地)
      
      - **Dilemma**: First, we fine-tune the parameters in the last stage, the model may meet the forgetting issue and be incapable to incorporate historical patterns. Second, if we do not use the parameters from the last stage and reinitialize the parameters, we lost rich historical information encoded in old parameters while suffering from low convergence speed.
      - Meta-learning is proposed as a solution as it can  incorporate historical information and learn better parameter  initialization to enable fast model adaption.
    - Solution Overview
    
      - Training Embeddings
      - Training Transfer Modules
      - Estimation Process
    
      

> 个人的一些疑问:
>
> - 一般来说，模型的时效性结束之后，都应该会使用 fine-tuning方法重新训练，为什么文章要重新介绍三种 retaining 方法呢？是因为我对这个领域缺乏了什么背景知识吗？这是一篇CCF-A文章！或者说当前方法与直接微调有何不同？
> - 使用NSCT来做网络数据检测，那么这些数据集要在哪里找呢？假设已经找到了数据，又该如何转成 NMT 格式呢?
> - Fine-tuning 与 retrain on new data 之间的差别仅仅前者使用已训练模型参数来做初始化而已吗？元学习在这项实验之中的作用是令模型学习如何初始化。
> - 为什么使用哈达玛积(element-wise)来做乘法，根据文章的公式，A、B、C三个矩阵行数看起来是允许不一样的，但是列数一定要相同？
> - 少样本学习 N-ways，K-shot 模仿人类快速学习新事物的能力，只需要少量样本即可使得模型快速识别新类别的能力，主要用于解决 Novel class 样本量过少的问题，非常符合这个场景；不过，这是怎么做到的呢？
> - 文章提到了 Transfer Module 能够提取历史数据，这是怎么实现的？


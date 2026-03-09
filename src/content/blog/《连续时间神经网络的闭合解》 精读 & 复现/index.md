---
title: '连续时间神经网络的闭合解 精读 & 复现'
publishDate: 2026-03-06
updatedDate: 2026-03-06
description: '这篇文章随便找的 测试一下功能'
tags:
  - 科研
  - 论文复现
  - 论文精读
language: 'English'
heroImage: { src: './lwbt.png', color: '#292626' }
---

## 📌 连续时间神经网络的闭合解

> 原标题：*Closed-form Continuous-time Neural Networks*  
> 来源：Nature Machine Intelligence, Vol.4, Nov 2022  
> 作者：Ramin Hasani, Mathias Lechner et al. (MIT)  
> 代码：https://github.com/raminmh/CfC

---

## 零、前言

笔者在本学期正式加入了某课题组，在师姐的指点下开展了相当稚嫩的科研工作。
<br>在掌握 Python基础 & 一些课题相关的基本概念 后，笔者开始了科研生涯的第一篇论文精读 & 复现 （仍施工中，先找了个模板）

## 一、🗺️ 论文地图

### 1.1 一句话概括本论文
> 通过对LTC（液态时间常数网络）的ODE（数值微分方程）求近似闭合解，可得到一种不需要数值求解器、性能（尤其是时间序列建模）优良1到5个数量级的连续时间神经网络。

### 1.2 论文解决了什么问题？
- **背景问题：连续时间神经网络的表达能力受数值微分方程求解器限制**
- **本文的解决思路：用闭合形式高效地近似求解。**

### 1.3 论文的核心贡献
1. 对LTC的动力学方程的积分给出了紧界近似闭合解。
2. 基于此设计了新的神经网络架构CfC（不需要任何 ODE 数值求解器，性能卓越）

### 1.4 和我课题的关联
笔者所研究课题的本质是不规则时间序列建模。
而本论文的成果：
<br>✅ 可处理不规则采样的时间序列数据
<br>✅ 可处理缺失数据的时序预测 
<br>  （时间 t 显式出现 → 天然支持任意时间间隔的输入，缺失数据不需要插值填补）
<br>✅ 比传统 RNN/LSTM 更快、更准确

---

## 二、📖 逐节精读笔记

> 格式说明：
> - 💬 原文引用（英文）
> - 📝 我的翻译
> - 🧠 我的理解/类比
> - ❓ 我的疑问
> - ⭐ 重要程度标记
> - ✏️ 关键概念/生词
---

### 2.1 摘要（Abstract）

#### 句1
💬 *"Continuous-time neural networks are a class of machine learning systems that can tackle representation learning on spatiotemporal decision-making tasks."*

📝 **连续时间神经网络是一类机器学习系统，能够处理时空决策任务上的表示学习。**

🧠 **理解：引入了本文的背景：连续时间神经网络 & 时空决策任务**

✏️ **Continuous-time neural networks：把神经网络的状态用连续的微分方程描述，而不是离散的"一步一步"更新。就像描述水流用流体方程，而不是每秒拍一张照片。**

✏️ **Representation learning（表示学习）：让模型自动学会"如何描述数据"。比如把原始风速数值，自动转化为"这是一个台风前兆"这样的高层特征**

✏️ **Spatiotemporal（时空的）：同时包含空间（哪个地点）和时间（什么时刻）两个维度。**

---

#### 句2
💬 *"These models are typically represented by continuous differential equations."*

📝 **这类模型通常用连续微分方程来表示。**

---

#### 句3
💬 *"However, their expressive power when they are deployed on computers is bottlenecked by numerical differential equation solvers."*

📝 **然而，当这些模型部署到计算机上时，它们的表达能力被数值微分方程求解器所瓶颈。**

🧠 **理解：为什么需要"数值求解器"？——大多数微分方程没有解析解（即无法直接写出公式）。计算机只能用近似的数值方法，一小步一小步地积分：**

---

#### 句4
💬 *"This limitation has notably slowed down the scaling and understanding of numerous natural physical phenomena such as the dynamics of nervous systems."*

📝 **这一限制显著减缓了对众多自然物理现象的规模化研究和理解，例如神经系统的动力学。**

✏️ **Nervous systems dynamics（神经系统动力学）：论文作者的研究背景是计算神经科学，他们受神经元放电的微分方程启发设计了 LTC/CfC 网络**

---

#### 句5
💬 *"Ideally, we would circumvent this bottleneck by solving the given dynamical system in closed form. This is known to be intractable in general."*

📝 **理想情况下，我们希望通过闭合形式求解给定的动力系统来绕过这一瓶颈。但这在一般情况下被认为是难以处理的。**

🧠 **理解：对于大多数非线性微分方程，数学家已经证明不存在能用初等函数写出的解析解。这就像"五次以上方程没有求根公式"一样，是数学上的根本限制。这是了本论文要解决的核心问题**

---

#### 句6
💬 *"Here, we show that it is possible to closely approximate the interaction between neurons and synapses—the building blocks of natural and artificial neural networks—constructed by liquid time-constant networks efficiently in closed form."*

📝 **在这里，我们证明：对于由液态时间常数网络构建的神经元与突触之间的交互——自然和人工神经网络的基本构件——可以用闭合形式高效地近似。**

🧠 **理解：这是本论文的核心贡献**

✏️ **Neurons（神经元）：网络中的计算单元 ; Synapses（突触）：神经元之间的连接**
✏️ **Liquid Time-Constant (LTC) networks：2021年同一团队提出的前作，用生物神经元的ODE方程建模，表达能力强但有上文提出的限制”**
✏️ **Closely approximate（紧密近似）：不是精确解，但误差有严格的数学上界（论文后面会证明）**

---

#### 句7 8 9
💬 *"To this end, we compute a tightly bounded approximation of the solution of an integral appearing in liquid time-constant dynamics that has had no known closed-form solution so far.This closed-form solution impacts the design of continuous-time and continuous-depth neural models. For instance, since time appears explicitly in closed form, the formulation relaxes the need for complex numerical solvers."*

📝 **为此，我们计算了液态时间常数动力学中出现的一个积分的紧有界近似，该积分迄今为止没有已知的闭合解。 这一闭合解影响了连续时间和连续深度神经模型的设计。例如，由于时间以显式形式出现在闭合解中，该公式消除了对复杂数值求解器的需求。**

🧠 **理解："时间显式出现"是什么意思？ <br>传统ODE方法：需要初始状态，在一步步积分后才能得出 t=10 时的状态 <br> 本文的CfC方法：直接把t=10 代入公式，瞬间得到答案**

---

#### 句10
💬 *"Consequently, we obtain models that are between one and five orders of magnitude faster in training and inference compared with differential equation-based counterparts."*

📝 **因此，与基于微分方程的对应模型相比，我们获得的模型在训练和推理速度上快了1到5个数量级。**

---

#### 句11 12
💬 *"More importantly, in contrast to ordinary differential equation-based continuous networks, closed-form networks can scale remarkably well compared with other deep learning instances. Lastly, as these models are derived from liquid networks, they show good performance in time-series modelling compared with advanced recurrent neural network models"*

📝 **更重要的是，与基于ODE的连续网络相比，闭合形式网络能够与其他深度学习实例相比出色地扩展。最后，由于这些模型源自液态网络，它们在时间序列建模方面表现出色。**

✏️ **scale：扩展**

---



### 2.2 引言（Introduction）

#### 第一段
💬 *"Continuous neural network architectures built by ordinary differential equations (ODEs) are expressive models useful in modelling data with complex dynamics. These models transform the depth dimension of static neural networks and the time dimension of recurrent neural networks (RNNs) into a continuous vector field, enabling parameter sharing, adaptive computations and function approximation for non-uniformly sampled data."*

📝 **常微分方程（ODE）构建的连续神经网络架构，是一类在对具有复杂动态特性的数据进行建模时非常有用的、表达力强的模型。 这类模型将静态神经网络的"层深度"维度，以及循环神经网络（RNN）的"时间"维度，统一转化为一个连续向量场，从而实现参数共享、自适应计算，以及对非均匀采样数据的函数近似。**

🧠 **对我课题的直接意义： 风场传感器数据天然就是非均匀采样的——有时数据密集，有时因为故障或遮挡出现缺失。这正是ODE类模型的强项所在。**

✏️ **ODE（Ordinary Differential Equation，常微分方程）：描述某个量随时间变化的速率的方程。**

✏️ **RNN（Recurrent Neural Network，循环神经网络）：处理序列数据的神经网络，有"记忆"，能把上一时刻的信息传到下一时刻。LSTM、GRU都是它的变体。**

✏️ **continuous vector field（连续向量场：空间中每个点都有一个箭头，表示"系统在这里会往哪个方向演化"。ODE神经网络用神经网络来定义这个场。**

✏️ **non-uniformly sampled data（非均匀采样数据）：数据采集的时间间隔不固定，比如传感器有时每秒采一次，有时断了好几秒才采一次**

---

#### 第二段
💬 *"These continuous-depth (time) models have shown promise in density estimation applications, as well as modelling sequential and irregularly sampled data."*

📝 **这些连续深度（时间）模型在密度估计应用中展现出了潜力，同时也在序列建模和不规则采样数据建模方面表现出色。**

✏️ **density estimation（密度估计）：估计数据的概率分布，比如"这个风速值出现的概率有多大"，常用于生成模型**

✏️ **sequential data（序列数据）：有先后顺序的数据，比如一段时间内的气温记录、文字句子等**

✏️ **continuous vector field（连续向量场：空间中每个点都有一个箭头，表示"系统在这里会往哪个方向演化"。ODE神经网络用神经网络来定义这个场。**

✏️ **irregularly sampled data（不规则采样数据）：和上面的"非均匀采样"同义，强调时间间隔不等、甚至有缺失。**

---

### 2.3 方法（Methods）

#### 核心公式记录

**公式编号：** 论文公式(X)

$$
在这里写LaTeX公式
$$

**各符号含义：**
| 符号 | 含义 | 取值范围/约束 |
|------|------|-------------|
| $$x(t)$$ | 神经元在t时刻的状态 | 实数向量 |
|      |      |             |

**公式直觉理解：**
> 用自己的话解释这个公式在"做什么"

**和上一个公式的关系：**

---

### 2.4 实验（Experiments）

#### 实验一：[实验名称]

| 项目 | 内容 |
|------|------|
| 数据集 | |
| 评价指标 | |
| 对比基线 | |
| 本文结果 | |
| 结论 | |

#### 实验二：[实验名称]
（同上格式）

---

### 2.5 结论（Conclusion）

**作者总结的贡献：**

**作者承认的局限性：**

**作者指出的未来方向：**

---

## 三、🔑 术语词典

> 每一个伟大的思想都有一个微不足道的开始

| 术语（英文） | 中文 | 定义 | 首次出现位置 |
|------------|------|------|------------|
| Closed-form solution | 闭合解 | 能用有限个初等函数表达的解析解 | 摘要第3句 |
| ODE | 常微分方程 | 描述函数及其导数关系的方程 | 摘要第2句 |
| | | | |

---

## 四、🧮 公式推导记录

> 把自己手推的过程记在这里，哪怕很粗糙

### 推导：从LTC的ODE到闭合解

**起点公式：**
$$\frac{dx}{dt} = \cdots$$

**第一步：**（写你的推导）

**第二步：**

**卡住的地方：**
> ❓ 第X步我不理解为什么可以这样变形，待查

---

## 五、💻 代码复现记录

### 5.1 环境配置

```bash
# 记录你实际用的命令
pip install ...
```

**遇到的问题及解决方案：**
- 问题1：`报错信息`
  - 解决：`解决方法`

---

### 5.2 核心模块理解

```python
# 粘贴关键代码，加上自己的注释
class CfCCell(nn.Module):
    def forward(self, x, h, t):
        # 这里对应论文公式(4)
        # gate = sigma(-f*t) 就是时间门控
        gate = torch.sigmoid(-f * t)
        ...
```

**代码和公式的对应关系：**
| 代码变量 | 论文符号 | 含义 |
|---------|---------|------|
| `gate`  | $$\sigma(-f \cdot t)$$ | 时间门控 |
|         |         |      |

---

### 5.3 实验复现结果

| 实验 | 论文报告值 | 我的复现值 | 差异原因分析 |
|------|----------|----------|------------|
| 人体活动识别 | 87.04% | | |
| Walker2D MSE | 0.617 | | |

---

## 六、🌱 拓展阅读清单

> 读这篇论文时发现需要补充的背景知识

- [ ] **LTC网络原论文**（本文的前作，必读）
  - Hasani et al., "Liquid Time-Constant Networks", AAAI 2021
- [ ] **Neural ODE**（理解ODE方法的基础）
  - Chen et al., NeurIPS 2018
- [ ] **LSTM**（对比基线，需要理解）
  - Hochreiter & Schmidhuber, 1997
- [ ] 补充数学：线性ODE的求解方法（变常数法）

---

## 七、💡 个人思考与创新想法

> 这一栏最重要！记录你自己的想法，哪怕很幼稚

### 7.1 我认为这篇论文最聪明的地方
>

### 7.2 我觉得还可以改进的地方
>

### 7.3 和我课题结合的想法
>

### 7.4 读完后新产生的问题
- 
- 

---

## 八、📊 论文评分（主观）

| 维度 | 评分（1-5） | 理由 |
|------|-----------|------|
| 创新性 | ⭐⭐⭐⭐⭐ | |
| 实验充分性 | ⭐⭐⭐⭐ | |
| 写作清晰度 | ⭐⭐⭐⭐ | |
| 和课题相关性 | ⭐⭐⭐⭐⭐ | |

---

## 九、📅 阅读日志

| 日期 | 完成内容 | 用时 | 备注 |
|------|---------|------|------|
| 2026-03-06 | 建立模板 | 2h | 无 |
| 2026-03-07 | 推进完摘要 & 引言前两段 | 3h | 术语较多，需复习 |
| | | | |
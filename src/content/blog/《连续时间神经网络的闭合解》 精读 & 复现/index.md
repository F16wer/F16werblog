---
title: '连续时间神经网络的闭合解 精读 & 复现'
publishDate: 2026-03-06
updatedDate: 2026-03-17
description: 'Here we go'
tags:
  - 科研
  - 论文复现
  - 论文精读
language: 'English'
heroImage: { src: './lwbt.png', color: '#292626' }
---

# 📌 论文基本信息

| 项目 | 内容 |
|------|------|
| **原标题** | Closed-form continuous-time neural networks |
| **作者** | Ramin Hasani, Mathias Lechner et al.(MIT) |
| **期刊** | Nature Machine Intelligence |
| **卷期页** | Volume 4, November 2022, pp. 992–1003 |
| **DOI** | https://doi.org/10.1038/s42256-022-00556-7 |
| **代码** | https://github.com/raminmh/CfC |

---

# 零、前言

笔者在本学期正式加入了某课题组，在师姐的指点下开展了相当稚嫩的科研工作。
<br>在掌握 Python基础 & 一些课题相关的基本概念 后，笔者开始了科研生涯的第一篇论文精读 & 复现 （仍施工中，先找了个模板）

# 一、🗺️ 论文地图

## 1.1 一句话概括本论文
> 通过对LTC（液态时间常数网络）的ODE（数值微分方程）求近似闭合解，可得到一种不需要数值求解器、性能（尤其是时间序列建模）优良1到5个数量级的连续时间神经网络。

## 1.2 论文解决了什么问题？
- **背景问题：连续时间神经网络的表达能力受数值微分方程求解器限制**
- **本文的解决思路：用闭合形式高效地近似求解。**

## 1.3 论文的核心贡献
1. 对LTC的动力学方程的积分给出了紧界近似闭合解。
2. 基于此设计了新的神经网络架构CfC（不需要任何 ODE 数值求解器，性能卓越）

## 1.4 和我课题的关联
笔者所研究课题的本质是不规则时间序列建模。
而本论文的成果：
<br>✅ 可处理不规则采样的时间序列数据
<br>✅ 可处理缺失数据的时序预测 
<br>  （时间 t 显式出现 → 天然支持任意时间间隔的输入，缺失数据不需要插值填补）
<br>✅ 比传统 RNN/LSTM 更快、更准确

---

# 二、📖 逐节精读笔记

> 格式说明：
> - 💬 原文引用（英文）
> - 📝 我的翻译
> - 🧠 我的理解/类比
> - ❓ 我的疑问
> - ⭐ 重要程度标记
> - ✏️ 关键概念/生词
---

## 2.1 摘要（Abstract）

### 核心意思：
- **问题：** 连续时间神经网络被ODE数值求解器拖慢
- **方法：** 对LTC动态中的关键积分做紧界近似，得到闭合形式解
- **结果：** 训练/推理快1~5个数量级，在时间序列任务上表现优异

---

### 精读

- **句1**

💬 *"Continuous-time neural networks are a class of machine learning systems that can tackle representation learning on spatiotemporal decision-making tasks."*

📝 **连续时间神经网络是一类机器学习系统，能够处理时空决策任务上的表示学习。**

🧠 **理解：引入了本文的背景：连续时间神经网络 & 时空决策任务**

✏️ **Continuous-time neural networks：把神经网络的状态用连续的微分方程描述，而不是离散的"一步一步"更新。就像描述水流用流体方程，而不是每秒拍一张照片。**

✏️ **Representation learning（表示学习）：让模型自动学会"如何描述数据"。比如把原始风速数值，自动转化为"这是一个台风前兆"这样的高层特征**

✏️ **Spatiotemporal（时空的）：同时包含空间（哪个地点）和时间（什么时刻）两个维度。**

---

- **句2**

💬 *"These models are typically represented by continuous differential equations."*

📝 **这类模型通常用连续微分方程来表示。**

---

- **句3**

💬 *"However, their expressive power when they are deployed on computers is bottlenecked by numerical differential equation solvers."*

📝 **然而，当这些模型部署到计算机上时，它们的表达能力被数值微分方程求解器所瓶颈。**

🧠 **理解：为什么需要"数值求解器"？——大多数微分方程没有解析解（即无法直接写出公式）。计算机只能用近似的数值方法，一小步一小步地积分：**

---

- **句4**

💬 *"This limitation has notably slowed down the scaling and understanding of numerous natural physical phenomena such as the dynamics of nervous systems."*

📝 **这一限制显著减缓了对众多自然物理现象的规模化研究和理解，例如神经系统的动力学。**

✏️ **Nervous systems dynamics（神经系统动力学）：论文作者的研究背景是计算神经科学，他们受神经元放电的微分方程启发设计了 LTC/CfC 网络**

---

- **句5**

💬 *"Ideally, we would circumvent this bottleneck by solving the given dynamical system in closed form. This is known to be intractable in general."*

📝 **理想情况下，我们希望通过闭合形式求解给定的动力系统来绕过这一瓶颈。但这在一般情况下被认为是难以处理的。**

🧠 **理解：对于大多数非线性微分方程，数学家已经证明不存在能用初等函数写出的解析解。这就像"五次以上方程没有求根公式"一样，是数学上的根本限制。这是了本论文要解决的核心问题**

---

- **句6**

💬 *"Here, we show that it is possible to closely approximate the interaction between neurons and synapses—the building blocks of natural and artificial neural networks—constructed by liquid time-constant networks efficiently in closed form."*

📝 **在这里，我们证明：对于由液态时间常数网络构建的神经元与突触之间的交互——自然和人工神经网络的基本构件——可以用闭合形式高效地近似。**

🧠 **理解：这是本论文的核心贡献**

✏️ **Neurons（神经元）：网络中的计算单元 ; Synapses（突触）：神经元之间的连接**

✏️ **Liquid Time-Constant (LTC) networks：2021年同一团队提出的前作，用生物神经元的ODE方程建模，表达能力强但有上文提出的限制”**

✏️ **Closely approximate（紧密近似）：不是精确解，但误差有严格的数学上界（论文后面会证明）**

---

- **句7 8 9**

💬 *"To this end, we compute a tightly bounded approximation of the solution of an integral appearing in liquid time-constant dynamics that has had no known closed-form solution so far.This closed-form solution impacts the design of continuous-time and continuous-depth neural models. For instance, since time appears explicitly in closed form, the formulation relaxes the need for complex numerical solvers."*

📝 **为此，我们计算了液态时间常数动力学中出现的一个积分的紧有界近似，该积分迄今为止没有已知的闭合解。 这一闭合解影响了连续时间和连续深度神经模型的设计。例如，由于时间以显式形式出现在闭合解中，该公式消除了对复杂数值求解器的需求。**

🧠 **理解："时间显式出现"是什么意思？ <br>传统ODE方法：需要初始状态，在一步步积分后才能得出 t=10 时的状态 <br> 本文的CfC方法：直接把t=10 代入公式，瞬间得到答案**

---

- **句10**

💬 *"Consequently, we obtain models that are between one and five orders of magnitude faster in training and inference compared with differential equation-based counterparts."*

📝 **因此，与基于微分方程的对应模型相比，我们获得的模型在训练和推理速度上快了1到5个数量级。**

---

- **句11 12**

💬 *"More importantly, in contrast to ordinary differential equation-based continuous networks, closed-form networks can scale remarkably well compared with other deep learning instances. Lastly, as these models are derived from liquid networks, they show good performance in time-series modelling compared with advanced recurrent neural network models"*

📝 **更重要的是，与基于ODE的连续网络相比，闭合形式网络能够与其他深度学习实例相比出色地扩展。最后，由于这些模型源自液态网络，它们在时间序列建模方面表现出色。**

✏️ **scale：扩展**

---



## 2.2 引言（Introduction）

### 研究背景:
ODE连续神经网络能建模复杂动态，但数值求解器限制了性能。

### 现有方法 & 不足（Research Gap）：

| 方法 | 思路 | 局限 |
|------|------|------|
| 状态增广（State Augmentation） | 降低ODE刚性 | 治标不治本 |
| 根查找法（Root-finding） | 重构前向传播 | 工程复杂 |
| 正则化方案 | 约束轨迹 | 精度损失 |
| Hypersolver | 加速求解器 | 仍依赖求解器框架 |

---
### 精读

- **第1段**

💬 *"Continuous neural network architectures built by ordinary differential equations (ODEs) are expressive models useful in modelling data with complex dynamics. These models transform the depth dimension of static neural networks and the time dimension of recurrent neural networks (RNNs) into a continuous vector field, enabling parameter sharing, adaptive computations and function approximation for non-uniformly sampled data."*

📝 **常微分方程（ODE）构建的连续神经网络架构，是一类在对具有复杂动态特性的数据进行建模时非常有用的、表达力强的模型。 这类模型将静态神经网络的"层深度"维度，以及循环神经网络（RNN）的"时间"维度，统一转化为一个连续向量场，从而实现参数共享、自适应计算，以及对非均匀采样数据的函数近似。**

🧠 **对我课题的直接意义： 风场传感器数据天然就是非均匀采样的——有时数据密集，有时因为故障或遮挡出现缺失。这正是ODE类模型的强项所在。**

✏️ **ODE（Ordinary Differential Equation，常微分方程）：描述某个量随时间变化的速率的方程。**

✏️ **RNN（Recurrent Neural Network，循环神经网络）：处理序列数据的神经网络，有"记忆"，能把上一时刻的信息传到下一时刻。LSTM、GRU都是它的变体。**

✏️ **continuous vector field（连续向量场：空间中每个点都有一个箭头，表示"系统在这里会往哪个方向演化"。ODE神经网络用神经网络来定义这个场。**

✏️ **non-uniformly sampled data（非均匀采样数据）：数据采集的时间间隔不固定，比如传感器有时每秒采一次，有时断了好几秒才采一次**

---

- **第2段**

💬 *"These continuous-depth (time) models have shown promise in density estimation applications, as well as modelling sequential and irregularly sampled data."*

📝 **这些连续深度（时间）模型在密度估计应用中展现出了潜力，同时也在序列建模和不规则采样数据建模方面表现出色。**

✏️ **density estimation（密度估计）：估计数据的概率分布，比如"这个风速值出现的概率有多大"，常用于生成模型**

✏️ **sequential data（序列数据）：有先后顺序的数据，比如一段时间内的气温记录、文字句子等**

✏️ **continuous vector field（连续向量场：空间中每个点都有一个箭头，表示"系统在这里会往哪个方向演化"。ODE神经网络用神经网络来定义这个场。**

✏️ **irregularly sampled data（不规则采样数据）：和上面的"非均匀采样"同义，强调时间间隔不等、甚至有缺失。**

---

- **第3段**

💬 *"While ODE-based neural networks with careful memory and gradient propagation design perform competitively with advanced discretized recurrent models on relatively small benchmarks, their training and inference are slow owing to the use of advanced numerical differential equation (DE) solvers.This becomes even more troublesome as the complexity of the data, task and state space increases (that is, requiring more precision), for instance, in open-world problems such as medical data processing, self-driving cars, financial time-series and physics simulations."*

📝 **尽管经过精心设计（记忆机制和梯度传播）的ODE神经网络，在相对小规模的基准测试上能与先进的离散循环模型相媲美，但由于依赖高级数值微分方程求解器，它们的训练和推理速度非常慢。随着数据、任务和状态空间复杂度的增加（即需要更高精度），这个问题变得更加棘手——例如医疗数据处理、自动驾驶、金融时间序列和物理仿真等现实场景。**

✏️ **discretized recurrent models（离散循环模型）：就是普通的LSTM、GRU，把时间切成一步一步处理，不用ODE求解器，速度快**

✏️ **sgradient propagation（梯度传播）：神经网络训练的核心机制——通过反向传播算法，把"预测错了多少"的信号从输出层传回输入层，用来更新参数**

---

- **第4段**

💬 *"The research community has developed solutions for resolving this computational overhead and for facilitating the training of neural ODEs, for instance by relaxing the stiffness of a flow by state augmentation techniques, reformulating the forward pass as a root-finding problem, using regularization schemes or improving the inference time of the network."*

📝 **研究界已提出了一些方案来解决这个计算开销问题，例如：通过状态增广降低流的刚性、将前向传播重新表述为求根问题、使用正则化方案、或改进网络的推理时间。**

🧠 **理解：这些方案都是在给求解器打补丁，治标不治本。**

---

- **第5段**

💬 *"Here, we derive a closed-form continuous-depth model that has the modelling capabilities of ODE-based models but does not require any solver to model data (Fig. 1)."*

📝 **本文推导出一个闭合形式的连续深度模型，它具备ODE模型的建模能力，但完全不需要任何求解器来对数据建模。**

---

- **第6段**

💬 *"Intuitively, in this work, we replace the integration (that is, solution) of a nonlinear DE describing the interaction of a neuron with its input nonlinear synaptic connections, with their corresponding nonlinear operators.This could be achieved in principle using functional Taylor expansions (in the spirit of the Volterra series). However, in the particular case of liquid time-constant (LTC) networks, we can leverage a closed-form expression for the system's response to input.This allows one to evaluate the system's response to exogenous input (I) and recurrent inputs from hidden states (x) as a function of time. One way of looking at this is to regard the closed-form solution as the application of a nonlinear forward operator to the inputs of each hidden state or neuron in the network, where the outputs of one neuron constitute the inputs for others.Effectively, this rests on approximating a conductance-based model with a neural mass model, of the kind used in the dynamic causal modelling of real neuronal networks."*

📝 **直觉上，本文的做法是：将描述神经元与其非线性突触连接相互作用的非线性微分方程的积分求解过程，替换为对应的非线性算子。一般情况下这可以通过泛函Taylor展开（Volterra级数的思路）来实现。但对于液态时间常数（LTC）网络这一特殊情况，我们可以直接利用一个闭合形式表达式来描述系统对输入的响应。这使得我们能够将 系统对外部输入 I 和 来自隐藏状态 x 的循环输入的响应，表示为以时间为自变量的函数。可以把这个闭合形式解理解为：对网络中每个隐藏状态或神经元的输入，施加一个非线性前向算子，其中一个神经元的输出构成另一个神经元的输入。本质上，这依赖于用神经质量模型来近似电导模型——这种方法在真实神经网络的动态因果建模中已有应用。**

---

- **第7段**

💬 *"The proposed continuous neural networks yield considerably faster training and inference speeds while being as expressive as their ODE-based counterparts.We provide a derivation for the approximate closed-form solution to a class of continuous neural networks that explicitly models time.We demonstrate how this transformation can be formulated into a novel neural model and scaled to create flexible, performant and fast neural architectures on challenging sequential datasets."*

📝 **所提出的连续神经网络在训练和推理速度上大幅提升，同时保持与ODE类模型相当的表达能力。我们为一类显式建模时间的连续神经网络，提供了近似闭合形式解的推导过程。我们展示了如何将这一变换构建成一个新颖的神经网络模型，并将其扩展，在具有挑战性的序列数据集上创建灵活、高性能且快速的神经网络架构。**

✏️ **derivation ： 推导**

✏️ **explicitly models time（显式建模时间）：时间 t 直接出现在公式里**

---

- **第8段**

💬 *"The proposed continuous neural networks yield considerably faster training and inference speeds while being as expressive as their ODE-based counterparts.We provide a derivation for the approximate closed-form solution to a class of continuous neural networks that explicitly models time.We demonstrate how this transformation can be formulated into a novel neural model and scaled to create flexible, performant and fast neural architectures on challenging sequential datasets."*

📝 **所提出的连续神经网络在训练和推理速度上大幅提升，同时保持与ODE类模型相当的表达能力。我们为一类显式建模时间的连续神经网络，提供了近似闭合形式解的推导过程。我们展示了如何将这一变换构建成一个新颖的神经网络模型，并将其扩展，在具有挑战性的序列数据集上创建灵活、高性能且快速的神经网络架构。**

✏️ **derivation ： 推导**

✏️ **explicitly models time（显式建模时间）：时间 t 直接出现在公式里**

---
## 2.3 理论准备

### 🔷 ① 推导神经元交互的近似闭合形式解

#### 本节目的：
建立生物学动机，推导LTC的ODE方程，并针对∫₀ᵗ f(I(s)) ds给出积分近似策略。

#### 三种被深度学习忽略的生物学机制：

| 机制 | 生物学含义 | 对应公式 |
|------|-----------|---------|
| ① 神经动态是连续过程 | 膜电位是连续变化的 | $$\frac{dx(t)}{dt} = -\frac{x(t)}{\tau} + S(t)$$ |
| ② 突触释放是非线性的 | 突触释放远不止是标量权重 | $$S(t)=f(I(t))(A-x(t))$$ |
| ③ 信息传播有反馈和记忆 | $$(A-x(t))$$ 构成负反馈 | 驱动力随膜电位升高而减小 |

#### LTC的完整ODE方程：

$$\frac{dx(t)}{dt} = -\left[\frac{1}{\tau} + f(I(t))\right]x(t) + f(I(t)) \cdot A$$


#### 闭合形式解：

$$x(t) = \left(x(0) - A\right) \cdot e^{-\left[\frac{t}{\tau} + \int_0^t f(I(s))ds\right]} \cdot f(-I(t)) + A$$

#### 🎯求解思路（逻辑链）：

```
对完整ODE方程套用线性ODE通解
       ↓
解中出现难以处理的积分：∫₀ᵗ f(I(s)) ds
       ↓
I(s) 任意定义 → 无法直接求闭合形式
       ↓
将 I(s) 离散化为分段常数 → 积分 ≈ 求和
       ↓
当积分作为指数衰减的指数时，近似可证明紧
       ↓
得到图1右侧闭合形式解
```

---

#### 精读

- **第一部分：三种生物学机制**

💬 *"Two neurons interact with each other through synapses as shown in Fig. 1. There are three principal mechanisms for information propagation in natural brains that are abstracted away in the current building blocks of deep learning systems."*

📝 **如图1所示，两个神经元通过突触相互作用。自然大脑中有三种核心的信息传播机制，而这些机制在当前深度学习的基本构件中被抽象掉了（即被忽略了）。**

🧠 **"abstracted away"是关键词——作者在说：普通神经网络为了简单，把这三种机制都扔掉了，而LTC/CfC把它们找回来了。**

---

- **机制① 神经动态是连续过程**

💬 *"(1) neural dynamics are typically continuous processes described by DEs (see the dynamics of x(t) in Fig. 1)"*

📝 **神经元膜电位x(t) 随时间连续变化，由微分方程描述(见图一中x(t)的方程)。**

对应图1中间的方程：

$$\frac{dx(t)}{dt} = -\frac{x(t)}{\tau} + S(t)$$

| 符号 | 含义 |
|------|------|
| $x(t)$ | 突触后神经元的膜电位（随时间变化） |
| $\tau$ | 神经元的时间常数，控制"遗忘速度"，τ 越大，记忆越持久 |
| $S(t)$ | 突触电流，来自突触前神经元的输入 |

 🧠 **这个方程说的是"膜电位的变化速率 = 自然衰减 + 外部输入"。左边是变化速率，右边第一项是神经元自己慢慢"忘掉"激活（衰减），第二项是突触带来的新刺激。**


---

- **机制② 突触释放是非线性的**

💬 *"(2) synaptic release is much more than scalar weights, involving a nonlinear transmission of neurotransmitters, the probability of activation of receptors and the concentration of available neurotransmitters, among other nonlinearities (see S(t) in Fig. 1)"*

📝 **突触释放远不止是一个标量权重，它涉及神经递质的非线性传递、受体激活概率、可用神经递质浓度等复杂非线性过程。**

对应图1左下角的公式：

$$S(t) = f(I(t)) \cdot (A - x(t))$$

| 符号 | 含义 |
|------|------|
| $f(\cdot)$ | 突触释放非线性函数，通常是 sigmoid，把输入压缩到 (0,1) |
| $I(t)$ | 突触前神经元的输入刺激 |
| $A$ | 突触反转电位（synaptic reversal potential），一个生物学常数 |
| $(A - x(t))$ | 驱动力（driving force），膜电位离反转电位越远，突触电流越大 |

🧠 **普通神经网络里，"突触"就是一个数字（权重 $w$）。而这里，突触是一个依赖输入和当前状态的非线性函数，表达力强得多。**
 
---

- **机制③ 信息传播有反馈和记忆**

💬 *"(3) the propagation of information between neurons is induced by feedback and memory apparatuses (see how I(t) stimulates x(t) through a nonlinear synapse S(t) which also has a multiplicative difference of potential to the postsynaptic neuron accounting for a negative feedback mechanism)."*

📝 **神经元间的信息传播由反馈和记忆机制驱动。I(t) 通过非线性突触 S(t) 影响 x(t)，而 S(t) 中的 (A−x(t)) 项构成一个负反馈机制。**

🧠 **当 x(t) 越来越大、接近 A 时，(A−x(t)) 越来越小，突触电流 S(t) 自动减弱——神经元不会无限激活，系统自动稳定。这是大脑的自我保护机制。**
 
---

- **过渡：从生物学到LTC**

💬 *"One could read I(t) as a mixture of exogenous input to the (neural) network and presynaptic inputs from other neurons that result in a depolarization x(t). This depolarization is mediated by the current S(t) that depends upon depolarization and a reversal threshold A.LTC networks, which are expressive continuous-depth models obtained by a bilinear approximation of a neural ODE formulation, are designed on the basis of these mechanisms.Correspondingly, we take their ODE semantics and approximate a closed-form solution for the scalar case of a postsynaptic neuron receiving an input stimulus from a presynaptic source through a nonlinear synapse."*

📝 **I(t) 可以理解为：网络的外部输入与来自其他神经元的突触前输入的混合，共同导致膜电位去极化x(t)。这个去极化由电流 S(t) 介导，而 S(t) 依赖于去极化程度和反转阈值 A。LTC网络是基于上述三种机制设计的，它是通过对神经ODE公式进行双线性近似得到的表达力强的连续深度模型。相应地，我们采用LTC的ODE语义，为标量情形（单个突触后神经元通过非线性突触接收突触前刺激）推导近似闭合形式解。**

🧠 **当 x(t) 越来越大、接近 A 时，(A−x(t)) 越来越小，突触电流 S(t) 自动减弱——神经元不会无限激活，系统自动稳定。这是大脑的自我保护机制。**

✏️ **depolarization（去极化）:神经元静息时膜电位为负（约-70mV），受刺激后电位升高（变得"不那么负"），这个过程叫去极化，是神经元"被激活"的过程**

✏️ **reversal threshold A（反转阈值）:当膜电位达到A 时，离子流方向反转，突触电流变为零——这是一个平衡点**

✏️ **bilinear approximation（双线性近似）:把S(t)=f(I(t))(A−x(t)) 中的乘积项视为"关于 f(I(t)) 和 x(t) 各自线性"的近似，使方程变得可处理**

✏️ **scalar case（标量情形）:先从最简单的单个神经元开始推导，后面再推广到整个网络。这是数学推导的标准策略：先解决简单情形，再扩展。**

---
- **第二部分：数学推导思路**

💬 *"To this end, we apply the theory of linear ODEs to analytically solve the dynamics of an LTC DE as shown in Fig. 1. We then simplify the solution to the point where there is one integral left to solve."*

📝 **为此，我们应用线性ODE理论解析求解图1所示的LTC微分方程，然后将解化简，直到只剩下一个积分需要求解。**

把 $S(t) = f(I(t))(A - x(t))$ 代入原方程：

$$\frac{dx(t)}{dt} = -\frac{x(t)}{\tau} + f(I(t))(A - x(t))$$

整理后：

$$\frac{dx(t)}{dt} = -\left[\frac{1}{\tau} + f(I(t))\right] x(t) + f(I(t)) \cdot A$$

这是一个关于 $x(t)$ 的**一阶线性 ODE**，套用线性 ODE 的通解公式，解为：

$$x(t) = (x(0) - A)\, e^{-\left[\frac{t}{\tau} + \int_0^t f(I(s))\,ds\right]} \cdot f(-I(t)) + A$$

🧠 **这正是图1右侧给出的闭合形式解！其中唯一"麻烦"的部分就是指数上的积分 $\int_0^t f(I(s))\,ds$。**

---

- **积分求解的问题**

💬 *"This integral compartment, $\int_0^t f(I(s))\,ds$, in which $f$ is a positive,continuous, monotonically increasing and bounded nonlinearity, is challenging tosolve in closed form since it has dependencies on an input signal $I(s)$ that isarbitrarily defined (such as real-world sensory readouts)."*

📝 **这个积分 $\int_0^t f(I(s))\,ds$ 中，$f$ 是正的、连续的、单调递增且有界的非线性函数。由于它依赖于任意定义的输入信号 $I(s)$（比如真实世界的传感器读数），很难求出闭合形式解。**

**🧠 为什么 f 要满足这些条件？**

| 条件 | 原因 |
|------|------|
| 正（positive） | 保证突触电流方向一致，不会出现负的突触释放 |
| 连续（continuous） | 保证数学推导中积分存在 |
| 单调递增（monotonically increasing） | 输入越强，释放越多，符合生物学直觉 |
| 有界（bounded） | 突触释放量有上限，防止数值爆炸 |

**🔑为什么这个积分难解？**
想象I(s) 是一段真实的风速数据——它的形状完全不规则，没有任何数学规律，所以$\int_0^t f(I(s))\,ds$ 根本没有通用的解析公式。

---

- **积分求解的思路**

💬 *"To approach this problem, we discretize $I(s)$ into piecewise constant segmentsand obtain the discrete approximation of the integral in terms of the sum ofpiecewise constant compartments over intervals."*

📝 **为解决这个问题，我们将 $I(s)$ 离散化为分段常数，把积分近似为各段上分段常数的求和。**

图示理解

I(s) 的真实形状（任意曲线）：<br>
```
     ╭──╮    ╭─╮
    ╭╯  ╰╮  ╭╯ ╰╮
────╯    ╰──╯   ╰────
```

分段常数近似（阶梯形）：
```
     ████    ██
    █████  ████
────█████████████────
  t0  t1  t2  t3  t4
```

每段内 $I(s) \approx I_k$（常数），则：

$$\int_0^t f(I(s))\,ds \approx \sum_k f(I_k) \cdot \Delta t_k$$

---

- **结尾**

💬 *"This piecewise constant approximation inspired us to introduce an approximate closed-form solution for the integral f(I(s))ds that is provably tight when the integral appears as the exponent of an exponential decay, which is the case for LTCs.We theoretically justify how this closed-form solution represents LTCs' ODE semantics and is as expressive (Fig. 1)."*

📝 **这个分段常数近似启发我们引入一个近似闭合形式解，当该积分作为指数衰减的指数出现时，这个近似是可证明紧的——而这正好是LTC的情形。我们从理论上证明了这个闭合形式解能够表示LTC的ODE语义，且具有同等的表达能力。**

💡 **为什么"作为指数的指数"时近似特别准？**
因为指数函数 $$e^{-x}$$ 在 $$x$$ 较大时变化非常平缓，对 $$x$$ 的小误差不敏感。换句话说，即使积分的近似有一点点偏差，$$e^{-\text{积分}}$$ 的值几乎不变——误差被指数函数"压缩"了。


---

### 🔷 ② — CfC 的效率优势.

#### 本节目的：
 用复杂度分析量化CfC相对于ODE求解器的速度优势。

#### 关于Table 1 

<br>
左半部分：求解器时间复杂度

| 方法 | 复杂度 | 局部误差 | 备注 |
|------|--------|----------|------|
| p阶求解器 | O(Kp) | O(ε^(p+1)) | 阶数越高越精确但越慢 |
| 自适应步长求解器 | — | O(ε̃^(p+1)) | 自动调步长，复杂度不定 |
| Euler 超求解器 | O(K) | O(δε²) | 最简单的求解器 |
| p阶超求解器 | O(Kp) | O(δε^(p+1)) | 改进版，仍有误差 |
| **CfC（本文）** | **O(K̃)** | **Not relevant** | ✅ 无求解器，无误差概念 |

右半部分：序列建模复杂度

| 模型 | 序列预测 | 逐步预测 | 瓶颈 |
|------|----------|----------|------|
| RNN | O(nk) | O(k) | ✅ 每步只做矩阵乘法，线性扩展 |
| ODE-RNN | O(nkp) | O(kp) | 每步还要跑ODE求解器，多乘一个 p |
| Transformer | O(n²k) | O(nk) | 自注意力机制需要每个位置关注所有其他位置，n²爆炸 |
| **CfC** | **O(nk)** | **O(k)** | ✅ 与 RNN 同级，无额外开销,但有连续时间表达能力 |


💡 三个关键概念

#### 1. 显式时间依赖（Explicit Time Dependence）
| | ODE 网络 | CfC |
|---|---|---|
| 时间处理方式 | 藏在微分里，需逐步积分 | t 直接出现在公式里 |
| 获得 x(t) 的方法 | 从 t=0 一步步算到 t | 直接代入 t 计算 |
| 类比 | 每小时模拟一次天气才知道明天结果 | 直接用公式算出明天天气 |

#### 2. K 与 K̃ 的区别
| 变量 | 含义 | 典型值（以10秒数据为例）|
|------|------|------------------------|
| K̃ | 实际输入数据点数 | 10 个 |
| K | ODE 求解器内部步数 | 1000~10000 个 |
| p | 求解器阶数（如RK4） | 4 |

> 复杂度差距：O(Kp) = 40000 次 vs O(K̃) = 10 次 → **差 4000 倍**

<br>

#### 3. 为什么 CfC 没有"近似误差"
- ODE 求解器：**每次推理**都在做数值近似，误差随步数累积
- CfC：近似只在**推导阶段**做了一次，之后每次推理都是精确计算该公式
- 类比：π ≈ 3.14159 是一次性近似，之后每次用它算圆面积都是精确的

<br>

#### 序列预测效率

<br>

两种预测模式
| 模式 | 输入 | 输出 | 应用场景 |
|------|------|------|----------|
| 序列预测 | 整段序列 [x₁,...,xₙ] | 一个标签/结果 | 读完整段再判断 |
| 逐步预测（自回归） | 当前时刻 xₜ | 下一时刻 xₜ₊₁ | 实时预测下一步 |

结论
> CfC = **ODE 的表达能力** + **RNN 的计算效率**

<br>

#### CfC 作为灵活深度模型

| 特性 | 说明 |
|------|------|
| 时间依赖门控 | σ(-f·t) 随时间变化，动态控制记忆，类似 LSTM 遗忘门但更灵活 |
| 混合记忆架构（mmRNN） | CfC 嵌入 LSTM，LSTM 细胞状态负责长期记忆，缓解梯度消失 |
| 效率提升 | 单位计算时间内精度比 ODE 模型提升 **超过 150 倍** |

---

#### 🎯 逻辑链

```
闭合解中 t 显式出现
        ↓
不需要 ODE 求解器迭代
        ↓
┌──────────────────────────────────┐
│ 优势1：复杂度从O(Kp)降到O(K̃)   │ ← K̃ 比 K 小 1000 倍
│ 优势2：无数值误差/不稳定性      │ ← 运行时不做近似
│ 优势3：训练推理快 10~150 倍     │ ← 实验验证
└──────────────────────────────────┘
        ↓
同时保持 ODE 的连续时间表达能力
        ↓
= RNN 的速度 + ODE 的表达力  ✅
```

---

#### 精读

- **🔵 显式时间依赖**

💬 *"We then dissect the properties of the obtained closed-form solution and design a new class of neural network models we call closed-form continuous-depth networks (CfC).CfCs have an explicit time dependence in their formulation that does not require a numerical ODE solver to obtain their temporal rollouts."*

📝 **我们接下来剖析所得闭合解的性质，并设计一类新的神经网络模型，称之为闭合形式连续深度网络（CfC）。CfC 的公式中显式地包含时间变量 t，因此不需要数值ODE求解器来展开其时间序列。**

🧠 **"显式时间依赖"（Explicit Time Dependence）是什么意思？**

**对比两种情况：**

| **类型** | **例子** | **时间如何处理** |
|---|---|---|
| 隐式时间依赖（ODE 网络） | $$\dfrac{dx}{dt} = f(x, t)$$ | 时间藏在微分里，必须一步一步数值积分才能知道 $$x(t)$$ |
| 显式时间依赖（CfC） | $$x(t) = \sigma(-f \cdot t) \odot g + (1 - \sigma(-f \cdot t)) \odot h$$ | $$t$$ 直接出现在公式里，给定 $$t$$ 直接算出 $$x(t)$$ |
**生活类比：**

- **ODE 方式** = 你想知道明天的天气，必须从今天开始，每隔 1 小时模拟一次大气变化，算 24 步才能得到答案
- **CfC 方式** = 你有一个公式 $$\text{weather}(t)$$，直接把 $$t = 24\text{h}$$ 代入，瞬间得到答案

**这就是为什么 CfC 不需要 ODE 求解器！** $$t$$ 已经在公式里了，直接代入计算即可。

**🔑为什么这个积分难解？**
想象I(s) 是一段真实的风速数据——它的形状完全不规则，没有任何数学规律，所以$\int_0^t f(I(s))\,ds$ 根本没有通用的解析公式。

---

💬 *"Thus, they maximize the trade-off between accuracy and efficiency of solvers.Formally, this property corresponds to obtaining lower time complexity for models without numerical instabilities and errors as illustrated in Table 1 (left).Table 1 (left) shows that the complexity of a pth-order numerical ODE solver is O(Kp), where K is the number of ODE steps, while a CfC system requires O( K̃), where is the exogenous input time steps, which are typically one to three orders of magnitude smaller than K.Moreover, the approximation error of a pth-order numerical ODE solver scales with $\mathcal{O}(\epsilon^{p+1})$, whereas CfCs are closed-form continuous-time systems, thus the notion of approximation error becomes irrelevant to them."*

📝 **"因此，CfC 最大化了精度与求解效率之间的权衡。形式上，这一性质对应于在没有数值不稳定性和误差的情况下获得更低的时间复杂度，如表1（左）所示。表1（左）显示，p 阶数值ODE求解器的复杂度为 O(Kp)，而CfC系统只需 O(K̃)，其中 K̃是外部输入的时间步数，通常比 K 小1到3个数量级。 此外，p 阶数值ODE求解器的近似误差量级为 $\mathcal{O}(\epsilon^{p+1})$ 而CfC是闭合形式的连续时间系统，因此 "近似误差"的概念对它们而言根本不适用。这种显式时间依赖使得CfC在训练和推理时间上比ODE对应模型至少快一个数量级，且不损失精度。**

🧠 **K 与 K̃ 的区别**
| 变量 | 含义 | 典型值（以10秒数据为例）|
|------|------|------------------------|
| K̃ | 实际输入数据点数 | 10 个 |
| K | ODE 求解器内部步数 | 1000~10000 个 |
| p | 求解器阶数（如RK4） | 4 |

复杂度差距：O(Kp) = 40000 次 vs O(K̃) = 10 次 → **差 4000 倍**

🧠 **为什么 CfC 没有"近似误差"**
- ODE 求解器：**每次推理**都在做数值近似，误差随步数累积
- CfC：近似只在**推导阶段**做了一次，之后每次推理都是精确计算该公式
- 类比：π ≈ 3.14159 是一次性近似，之后每次用它算圆面积都是精确的

---
- **🔵 序列与单步预测效率**

💬 *"In sequence modelling tasks, one can perform predictions based on an entire sequence of observations, or perform auto-regressive modelling where the model predicts the next time-step output given the current time-step input."*

📝 **"在序列建模任务中，可以基于整个观测序列做预测（序列预测），也可以做自回归建模，即给定当前时间步输入预测下一时间步输出（逐步预测）。**

🧠 **序列预测 vs 逐步预测**

| **模式** | **输入** | **输出** | **类比** |
|---|---|---|---|
| 序列预测 | 整段序列 $$[x_1, x_2, \ldots, x_n]$$ | 一个标签 / 结果 | 读完整篇文章再做判断 |
| 逐步预测（自回归） | 当前时刻 $$x_t$$ | 下一时刻 $$x_{t+1}$$ | 每读一句话就预测下一句 |
对**风场预测**课题来说，两种模式都可能用到：

**序列预测** → 给定过去 N 小时风场，预测是否会发生风切变<br> 
**逐步预测** → 实时预测下一时刻风场状态

---

💬 *"We observe that the complexity of ODE-based networks and Transformer modules is at least an order of magnitude higher than that of discrete RNNs and CfCs in both frameworks.This is desirable because not only do CfCs establish a continuous flow similar to ODE models to achieve better expressivity in representation learning but they do so with the efficiency of discrete RNN models."*

📝 **"我们观察到，ODE网络和Transformer的复杂度比离散RNN和CfC高至少一个数量级。这是令人期待的，因为CfC不仅建立了类似ODE模型的连续流以实现更好的表示学习表达能力，而且是以离散RNN模型的效率做到这一点的。**

🧠 **Table 1 右半部分逐行解读**
 设序列长度为 $$n$$，隐藏单元数为 $$k$$，ODE 求解器阶数为 $$p$$：

| **模型** | **序列预测** | **逐步预测** | **直觉理解** |
|---|---|---|---|
| RNN | $$\mathcal{O}(nk)$$ | $$\mathcal{O}(k)$$ | 每步只做矩阵乘法，线性扩展 |
| ODE-RNN | $$\mathcal{O}(nkp)$$ | $$\mathcal{O}(kp)$$ | 每步还要跑 ODE 求解器，多乘一个 $$p$$ |
| Transformer | $$\mathcal{O}(n^2k)$$ | $$\mathcal{O}(nk)$$ | 自注意力机制需要每个位置关注所有其他位置，$$n^2$$ 爆炸 |
| CfC | $$\mathcal{O}(nk)$$ | $$\mathcal{O}(k)$$ | 和 RNN 一样高效，但有连续时间表达能力 |

**结论：CfC = ODE 的表达能力 + RNN 的计算效率**，这是它最吸引人的地方。

---

- **🔵 CfC：面向序列任务的灵活深度模型**

💬 *"CfCs are equipped with novel time-dependent gating mechanisms that explicitly control their memory."*

📝 **CfC配备了新颖的时间依赖门控机制，可以显式地控制其记忆。**

🧠 **这里指的是公式(4)中的 σ(−f⋅t) 项——它随时间变化，控制"记住多少旧信息"和"接受多少新信息"，类似LSTM的遗忘门，但更加动态。**

---

💬 *"CfCs are as expressive as their ODE-based peers and can be supplied with mixed memory architectures to avoid gradient issues in sequential data processing applications with long-range dependences."*

📝 **CfC与ODE对应模型具有同等的表达能力，并且可以与混合记忆架构结合，以避免长程依赖序列处理中的梯度问题。**

🧠 **什么是"混合记忆架构"（Mixed Memory Architecture）？**

这对应论文中的 CfC-mmRNN 变体。

核心思想：把CfC嵌入到LSTM里，让LSTM的细胞状态（cell state）充当长期记忆，CfC负责连续时间动态建模。

输入 → [CfC（连续时间动态）] → LSTM细胞状态（长期记忆） → 输出
<br>

🧠 **为什么需要这个？** 

CfC本身可能有梯度消失问题（序列很长时），LSTM的门控机制可以缓解这个问题。对长时间风场序列预测，这个变体可能特别有用。

---

💬 *"Our results indicate that, when considering accuracy per compute time, CfCs exhibit over 150 fold improvements over ODE-based compartments."*

📝 **我们的结果表明，在考虑单位计算时间内的精度时，CfC比ODE模块提升超过 150倍。**

🧠 **"accuracy per compute time"是什么指标？**

这不是单纯的精度，也不是单纯的速度，而是效率的综合衡量：

$$\text{效率} = \frac{\text{模型精度（Accuracy）}}{\text{所需计算时间（Training/Inference Time）}}$$

**举例：**

- **ODE 模型：** 精度 90%，训练 1 小时 → 效率 $$= 90/60 = 1.5$$ 分/分钟
- **CfC 模型：** 精度 91%，训练 1 分钟 → 效率 $$= 91/1 = 91$$ 分/分钟

这就是"150 倍提升"的来源——**CfC 不仅快，而且精度不降甚至更高**。
​

---
### 🔷 ③ — 闭合解的推导.

#### 本节目的：
给 LTC 网络的动力学方程找到一个"不需要ODE求解器、可以直接算出答案"的近似公式，并证明这个近似足够精确。

#### 🎯逻辑链：
```
LTC 的 ODE（精确但无法直接算）

        ↓  [线性 ODE 标准解法：常数变易法]

含积分的解析解（精确，但积分无法直接求）

        ↓  [限定：分段常数输入]

分段常数情况下的闭合解（精确）

        ↓  [推广：用 f(I(t))·t 近似 ∫f(I(s))ds]

任意输入下的近似闭合解 → 定理 1，公式 (2)

        ↓  [验证]

误差上界证明（紧性结果）+ 实践验证（MSE = 0.006）

```

#### LTC的ODE定义（公式1）

$$
\frac{d\mathbf{x}}{dt} = -\bigl[w_\tau + f(\mathbf{x}, \mathbf{I}, \theta)\bigr] \odot \mathbf{x}(t) + A \odot f(\mathbf{x}, \mathbf{I}, \theta)
\tag{1}
$$

#### 符号表

| 符号 | 维度 | 含义 |
|------|------|------|
| $\mathbf{x}(t)$ | $D\times1$ | 当前时刻隐藏状态向量（D个神经元） |
| $\mathbf{I}(t)$ | $m\times1$ | 外部输入信号（m个特征） |
| $w_\tau$ | $D\times1$ | 时间常数参数向量，控制衰减速度，**可学习** |
| $A$ | $D\times1$ | 偏置向量，神经元的目标平衡值，**可学习** |
| $f(\cdot;\theta)$ | — | 由 $\theta$ 参数化的神经网络（模拟突触非线性） |
| $\odot$ | — | Hadamard积（逐元素相乘） |

#### 方程结构拆解

$$\underbrace{\frac{d\mathbf{x}}{dt}}_{\text{状态变化率}} = \underbrace{-w_\tau \odot \mathbf{x}(t)}_{\text{自然衰减}} \underbrace{- f(\mathbf{x},\mathbf{I},\theta)\odot\mathbf{x}(t)}_{\text{输入调制衰减}} + \underbrace{A \odot f(\mathbf{x},\mathbf{I},\theta)}_{\text{突触驱动项}}$$

**直觉理解**

> 神经元状态像一个弹簧：总在向"平衡点 $A$"靠近，
> 但靠近的**速度**和**平衡点位置**都随输入 $I(t)$ 动态变化。
> 这就是"液态（Liquid）"的核心含义。

**为什么难以直接求解？**

- $f$ 依赖于 $\mathbf{x}(t)$（可能有循环连接），使ODE**非线性**
- 即使简化为单神经元、无循环连接，指数上仍含有积分 $\int_0^t f(I(s))ds$，无法直接计算

---

#### 定理1（Theorem 1）

**定理条件**

- 单个神经元（标量情况，$D=1$）
- 单维外部输入 $I(t)$
- 无自连接（$f$ 不依赖 $x(t)$）

**定理结论（公式2）**

$$
x(t) \approx (x_0 - A)\, e^{-[w_\tau + f(I(t),\theta)]\,t} \cdot f(-I(t), \theta) + A
\tag{2}
$$

**逐项理解**

| 项 | 表达式 | 含义 |
|---|---|---|
| 初始偏差 | $(x_0 - A)$ | 初始状态距平衡点的距离 |
| 指数衰减 | $e^{-[w_\tau + f(I(t),\theta)]\,t}$ | 以动态速率衰减，速率由当前输入决定 |
| 调制因子 | $f(-I(t), \theta)$ | 对负输入的非线性变换，起互补调制作用 |
| 平衡点 | $+ A$ | 系统长期趋向的目标值，$t\to\infty$ 时 $x(t)\to A$ |

#### ⚠️ 近似的本质

原本精确解中，指数上应为：

$$e^{-\int_0^t [w_\tau + f(I(s),\theta)]\,ds}$$

作者用以下替换完成近似：

$$\int_0^t f(I(s),\theta)\,ds \quad \longrightarrow \quad f(I(t),\theta) \cdot t$$

**为什么这个近似合理？**

- 误差出现在指数的指数位置
- 当 $w_\tau > 0$ 时，误差项被 $e^{-w_\tau t}$ 压制，**随时间指数衰减**
- 误差上界 $= |x_0 - A| \cdot e^{-w_\tau t}$（紧的，即最优界）

---

#### 精读

**🔵 数学推导 & 实践中的紧性检验**

💬 *"In this section, we derive an approximate closed-form solution for LTC networks, an expressive subclass of time-continuous models.We discuss how the scalar closed-form expression derived from a small LTC system can inspire the design of CfC models.In this regard, we define the LTC semantics.We then state the main theorem that computes a closed-form approximation of a given LTC system for the scalar case.To prove the theorem, we first find the integral solution of the given LTC ODE system.We then compute a closed-form analytical solution for the integral solution for the case of piecewise constant inputs.Afterward, we generalize the closed-form solution of the piecewise constant inputs to the case of arbitrary inputs with our novel approximation...and finally provide sharpness results (that is, measure the rate and accuracy of an approximation error) for the derived solution."*

📝 **在本节中，我们为LTC网络推导一个近似的闭合解，LTC网络是时间连续模型中一个表达能力很强的子类。我们讨论如何从一个小型LTC系统推导出的标量闭合表达式，来启发CfC模型的设计。为此，我们首先定义LTC的语义（即它的数学形式）。然后我们陈述主定理，该定理给出了标量情况下LTC系统的闭合近似解。为了证明该定理，我们首先求出LTC ODE系统的积分解。然后，我们针对分段常数输入的情况，计算出积分解的闭合解析解。之后，我们通过新颖的近似方法，将分段常数输入的闭合解推广到任意输入的情况。最后，为推导出的解提供紧性结果（即衡量近似误差的速率和精度）。**

🧠 **分段常数输入：把输入信号 I(t) 看成一段一段的常数（像楼梯一样），在每一小段内 I(t) 不变。这是一个特殊情况，在这种情况下积分  f(I(s))ds 可以被精确计算出来。**

---

💬 *"The hidden state of an LTC network is determined by the solution of the following initial value problem (IVP): "*
$$ 
\frac{d\mathbf{x}}{dt} = -\bigl[w_\tau + f(\mathbf{x}, \mathbf{I}, \theta)\bigr] \odot \mathbf{x}(t) + A \odot f(\mathbf{x}, \mathbf{I}, \theta)\tag{1} 
$$

📝 **LTC网络的隐藏状态由以下初值问题（IVP）的解决定。**

| **项** | **生物含义** | **数学作用** |
|--------|------------|------------|
| $\dfrac{dx}{dt}$ | 膜电位的变化速率 | 方程左边，我们要求解的量 |
| $-w_\tau \odot \mathbf{x}(t)$ | 神经元自然衰减 | 把状态拉向 0 |
| $-f(\mathbf{x}, \mathbf{I}, \theta) \odot \mathbf{x}(t)$ | 突触电导调制衰减 | 输入越强，衰减越快 |
| $A \odot f(\mathbf{x}, \mathbf{I}, \theta)$ | 突触驱动项 | 把状态拉向目标值 A |

💡 **直觉：** 这个方程就像一个弹簧——神经元状态总在往某个"平衡点"靠近，但这个平衡点和靠近速度都会随输入 $I(t)$ 动态变化，这就是"液态"的精髓。

---

💬 *"...where at a time step t, x(D×1)(t) defines the hidden state of a LTC layer with D cells, and I(m×1)(t) is an exogenous input to the system with m features.Here, w(D×1)τ is a time-constant parameter vector, A(D×1) is a bias vector, f is a neural network parametrized by θ and ⊙ is the Hadamard product.The dependence of f(.) on x(t) denotes the possibility of having recurrent connections."*

📝 **其中在时间步 t，$\mathbf{x}^{(D\times1)}(t)$ 定义了含 D 个神经元的 LTC 层的隐藏状态，$\mathbf{I}^{(m\times1)}(t)$ 是系统的外部输入，有 m 个特征。 这里 $w_\tau^{(D\times1)}$ 是时间常数参数向量，$A^{(D\times1)}$ 是偏置向量，$f$ 是由 $\theta$ 参数化的神经网络，$\odot$ 是 Hadamard 积。f(.) 对 x(t) 的依赖表示网络可以有循环连接。**

🧠 **上标 $(D \times 1)$ 说明这是一个列向量，D 是神经元数量。对风场而言，m 就是风速、风向、气压等传感器特征的数量。**

🧠 **$w_\tau$：控制每个神经元衰减的快慢，可学习**。

🧠 **$A$：每个神经元的"目标平衡值"，可学习。**

🧠 **Hadamard 积：就是两个同维向量逐元素相乘，例如 $[1,2] \odot [3,4] = [3,8]$，区别于矩阵乘法。**

🧠 **如果 $f$ 的输入包含当前隐藏状态 $\mathbf{x}(t)$，那么神经元之间就有反馈连接（即循环神经网络的特性）。这使得 ODE 更难求解，但表达能力更强。**

---

💬 *"The full proof of theorem 1 is given in Methods. The theorem formally demonstrates that the approximated closed-form solution for the given LTC system is given by equation (2) and that this approximation is tightly bounded with bounds given in the proof."*

📝 **定理1的完整证明在Methods部分给出。该定理正式证明了LTC系统的近似闭合解由公式(2)给出，且该近似的误差界在证明中被严格给出。**

---

💬 *"Figure 2 shows an LTC-based network trained for autonomous driving. The figure further illustrates how close the proposed solution fits the actual dynamics exhibited from a single-neuron ODE given the same parametrization.We next show how to design a novel neural network instance inspired by this closed-form solution that has well-behaved gradient properties and approximation capabilities."*

📝 **图2展示了一个为自动驾驶训练的LTC网络，进一步说明了在相同参数化下，所提出的解与单神经元ODE实际动态的吻合程度。接下来我们展示如何从这个闭合解出发，设计一个具有良好梯度性质和近似能力的新型神经网络实例。**

---



## 2.3 方法（Methods）

### 核心公式记录

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

## 九、📅 精读 & 复现日志

| 日期 | 完成内容 | 用时 | 备注 |
|------|---------|------|------|
| 2026-03-06 | 建立模板 | 2h | 无 |
| 2026-03-07 | 推进完摘要 & 引言前两段 | 3h | 术语较多，需复习 |
| 2026-03-16 & 17 | 推进完理论部分 | 8h | 数学基础需加强 |
| | | | |
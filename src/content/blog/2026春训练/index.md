---
title: '26春训练'
publishDate: 2026-02-20
updatedDate: 2026-02-20
description: '旧的不去，新的不来'
tags:
  - 算法竞赛训练
  - 规划
language: 'Chinese'
heroImage: { src: './LQB.png', color: '#83c7d5' }
---

上学期踩线过了ACM实验室的选拔，摆了大半个寒假也该干些正事了。

## 近期比赛

2026-3-14 团体程序设计天梯赛-校选拔赛

2026-3-21 浪潮杯校赛

2026-4-11 蓝桥杯A组省赛（c++）

2026-4-18 团体程序设计天梯赛-正赛



## 近期备赛安排

现水平还是很一般啊，而且老本也丢得差不多了，也没什么好啃的了。

目前的想法是以AcWing的[蓝桥杯每日训练](https://www.acwing.com/activity/content/introduction/2869)为路线进行一个基本的突击，各个板块先达到一个基本的“省赛够用”水平。
<br>强度的话，开学后一周5-7练，每天4-10h不定。

要是游出校赛了就继续备战省赛，要是淹死了就直接按下半年的ACM来练。

PS：明明直接备战ACM可能是提升水平更系统的方式，为什么我的策略如此“贪心”呢？
<br>——因为ZZU计院保研细则里加分的比赛只有《2023全国普通高校大学生竞赛分析报告》竞赛目录里关于计算机的那几个比赛，我现阶段 想 & 能 参加的大概就只有 蓝桥杯 & 天梯赛 了，我希望大一就拿出成绩，减轻些大二的压力
<br>——毕竟我的绩点已经爆了一半了，竞赛外还有科研要做。

## 训练日志

2/20 —— 前缀和 & 差分
<br> 前缀和 T1 [AcWing 3956. 截断数组](https://www.acwing.com/problem/content/3959/).
<br> 前缀和 T2 [AcWing 1230. K倍区间](https://www.acwing.com/problem/content/1232/).
<br> 差分 T1 [AcWing 3729. 改变数组元素](https://www.acwing.com/problem/content/3732/).

2/24 —— 二分
<br> T1 [AcWing 1460. 我在哪？](https://www.acwing.com/activity/content/punch_the_clock/2869/).
<br> T2 [AcWing 1221. 四平方和](https://www.acwing.com/problem/content/1223/).

2/25 —— 二分 
<br> 二分 T3 [AcWing 1227. 分巧克力](https://www.acwing.com/activity/content/problem/content/8030/).

3/5 —— 双指针
<br> T1 [AcWing 3768. 字符串删减](https://www.acwing.com/problem/content/3771/)
<br> T2 [AcWing 1238. 日志统计](https://www.acwing.com/problem/content/1240/)

PS：思来想去，决定这里只放题号，按月另开帖子po代码，全放这还是太挤了些。


## 一些经验/教训/值得记录的理解/备忘

1 不开longlong见祖宗。——统计类问题常开longlong统计结果，可存10的18次方量级的数据。

2 内存爆栈问题。——数组最好放全局变量那。对于256mb来说，longlong开1.5x10的7次方以内是安全的。

3 多测清空问题：
<br> 法1：memset(a + L, 0, (R - L + 1) * sizeof(a[0]));
<br> 法2：在多测内用vector
<br> 法3：直接覆盖

4 对于一些无法避免的枚举层数较多的问题（如[AcWing 1221](https://www.acwing.com/problem/content/1223/)），可以预先计算并存储。

5 可以考虑对 某组输入数据 依据某项值（如时间/大小）进行预处理（如排序）来简化后续处理 如sort(a,a+n).

## 已经复习过的算法复述

1 前缀和：通过b[i] = a[i] + a[i - 1] 实现对“前i项和”的统计，方便后续处理

2 差分：通过b[i] = a[i] - a[i - 1] 实现前缀和的逆运算。

3 二分：处理“有序数据”的查询，可用于解决“最大值最小化”/“最小值最大化”。
<br>一个基本的实现（保证左边界合法右边界不合法）
```c++
while (l + 1 < r) {       // 如果两边界不相邻
    int mid = (l + r) / 2;  
    if (check(mid))         
      l = mid;              
    else
      r = mid;  
  }
  return l;  // 返回左边界
```

4 双指针：在统计区间信息时，一般会“对每个节点往后计算区间长度”再统计，然而我们可以通过维护两端节点的信息来避免重复计算。

## 待办

1 同余性质等基本的数论学习（在实际比赛中往往是 部分分 到 全部分 的关键，例如AcWing 1230，不知道同余的话没办法优化到一层循环，我也是看了题解才写出来的）

2 重载运算符学习.
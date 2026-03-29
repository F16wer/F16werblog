---
title: '26春训练'
publishDate: 2026-02-20
updatedDate: 2026-03-29
description: '旧的不去，新的不来'
tags:
  - 算法竞赛训练
  - 规划
language: 'Chinese'
heroImage: { src: './LQB.png', color: '#83c7d5' }
---

上学期踩线过了ACM实验室的选拔，摆了大半个寒假也该干些正事了。

## 近期比赛


2026-3-21 浪潮杯校赛（已完结）

2026-4-11 蓝桥杯A组省赛（c++）

PS：原定的天梯赛因笔者选拔赛时身心不适退赛。

## 近期备赛安排

现水平还是很一般啊，而且老本也丢得差不多了，也没什么好啃的了。

目前的想法是以AcWing的[蓝桥杯每日训练](https://www.acwing.com/activity/content/introduction/2869)为路线进行一个基本的突击，各个板块先达到一个基本的“省赛够用”水平。


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

3/8 —— 递推
<br> T1 [AcWing 1208. 翻硬币](https://www.acwing.com/problem/content/1210/).

3/9 —— 最短路 
<br> T1 [Dijk基本模板](https://www.luogu.com.cn/problem/P4779).
<br> T2 [Dijk 的 堆优化 & 变体](https://www.luogu.com.cn/problem/U629163?contestId=295494).(密码:ey33).
<br> T3 [SPFA求负环基本模板](https://www.luogu.com.cn/problem/P3385).
<br> T4 [Floyd求传递闭包基本模板](https://www.luogu.com.cn/problem/B3611).

3/10 —— DFS & 蓝桥杯特训
<br> DFS T1[AcWing 3502. 不同路径数](https://www.acwing.com/problem/content/3505/).
<br>[	[蓝桥杯 2022 省 A] 填空问题](https://www.luogu.com.cn/problem/P8770).
<br>[ [蓝桥杯 2022 省 A] 求和](https://www.luogu.com.cn/problem/P8772).
<br>[ [蓝桥杯 2022 省 A] 选数异或](https://www.luogu.com.cn/problem/P8773).

3/11 —— 背包DP
<br> 背包DP T1（完全背包）[P1616 疯狂的采药](https://www.luogu.com.cn/problem/P1616).
<br> 背包DP T2（01背包）[P1833 樱花](https://www.luogu.com.cn/problem/P1833).

3/21 —— [浪潮杯校赛](https://f16wer.com/blog/%E6%B5%AA%E6%BD%AE%E6%9D%AF%E6%A0%A1%E8%B5%9B%E6%B8%B8%E8%AE%B0/).

3/28 & 29 —— [蓝桥杯 2025 省A c++](https://www.luogu.com.cn/training/748106#problems).（能力所限，只写简单题）
<br> [P12138 [蓝桥杯 2025 省 A] 寻找质数](https://www.luogu.com.cn/problem/P12138).
<br> [P12139 [蓝桥杯 2025 省 A] 黑白棋](https://www.luogu.com.cn/problem/P12139)
<br> [P12140 [蓝桥杯 2025 省 A] 抽奖](https://www.luogu.com.cn/problem/P12140)
<br> [P12141 [蓝桥杯 2025 省 A] 红黑树](https://www.luogu.com.cn/problem/P12141)
<br> [P12143 [蓝桥杯 2025 省 A] 好串的数目](https://www.luogu.com.cn/problem/P12143)
<br> [P12144 [蓝桥杯 2025 省 A] 地雷阵](https://www.luogu.com.cn/problem/P12144)

<br>PS：思来想去，决定这里只放题号，按月另开帖子po代码，全放这还是太挤了些。


## 一些经验/教训/值得记录的理解/备忘

“小细节”：
> <br>  不开longlong见祖宗。——统计类问题常开longlong统计结果，可存10的18次方量级的数据。<br>
> <br>  内存爆栈问题。——数组最好放全局变量那。对于256mb来说，longlong开1.5x10的7次方以内是安全的。<br>
> <br>  对于memset“上限”，一般填0x3f，但在程序当中检测“是否有更新过”时，则不是0x3f，实际大小是0x很多个3f，有效的检测办法是>1e18.<br>
> <br>  计数时，用 a[++cnt] 而不是 cnt+=1 可以有效防止“多加一次”问题.
> <br>  注意变量名字冲突问题，命名得有意义一些是一个有效的办法.
> <br>  深搜时每写一个dfs() 一定要在后面记得加return 和 复位.
> <br>  某值从1到n环形移动时,考虑边界的整除问题，正确的代码是a = (a + x - 1) % n + 1;

多测清空问题：
> <br> 对于数组：
> <br> 法1：memset(a + L, 0, (R - L + 1) * sizeof(数据类型));
> <br> 法2：直接覆盖<br>
> <br> 对于vector,要用for挨个.clear().<br>
> <br> 队列/栈：while(!q.empty（）)   q.pop();

异或运算：
> <br> 相同为0，不同为1.
> <br> 归零律：a⊕a=0 （自己和自己异或，底牌全消）
> <br> 恒等律：a⊕0=a （和 0 异或，保持真我）
> <br> 交换律与结合律：a⊕b⊕c=a⊕c⊕b （随便打乱顺序，结果不变）
> <br> 自反律：如果 a⊕b=c，那么必然有 a⊕c=b 和 b⊕c=a.

预处理：
> <br> 对于一些无法避免的枚举层数较多的问题（如[AcWing 1221](https://www.acwing.com/problem/content/1223/)），可以预先计算并存储。
> <br>
> <br> 可以考虑对 某组输入数据 依据某项值（如时间/大小）进行预处理（如排序）来简化后续处理 如sort(a,a+n).

数论:
> <br> 找质数要从2开始找.


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

5 单源最短路：通过不断的松弛操作，得到最短路.
<br>堆优化的一个模板.
```c++
struct node{
  long long dis,u;
  bool operator>(const node& a) const{return dis > a.dis;}
};
priority_queue<node vector<node>,greater<node>>;
```

6 排序STL模板
```c++
sort(v.begin(), v.end());//vector的排序（从小到大）
sort(v.begin(), v.end(), cmp);//自定义比较函数 （也有用结构体＋重载运算符的操作） 
sort(arr, arr + n);//普通数组排序 
sort(arr + 1, arr + 3);//局部排序

bool cmp(const Student& a, const Student& b) {
    if (a.score != b.score) {
        // 第一优先级：分数不同时，分数高的排前面（降序）
        return a.score > b.score; 
    } else {
        // 第二优先级：分数相同时，学号小的排前面（升序）
        return a.id < b.id; 
    }
}//以成绩排序为例的一个cmp函数模板
```

7 区间合并：遇到了如[P12144 [蓝桥杯 2025 省 A] 地雷阵](https://www.luogu.com.cn/problem/P12144)的题目，<br>存在多个区间而需要求解“覆盖了多少”的问题。<br>
可以通过：先对所有区间按左端点顺序进行排序，如排在后面的区间的左端点出现在了目前区间的右端点的前面——把目前区间的右端点更新至后面区间的右端点，在扫描完目前区间的右端点时把该区间合并，存至另外一个数组。<br>
简而言之：在排序完区间后维护右端点直到无重合且离开区间。<br>

8 三角函数：c++里的函数主要以弧度形式存在
| **函数名** | **数学含义** | **参数要求** | **返回值范围** |
| :--- | :--- | :--- | :--- |
| `sin(x)` | 正弦 $$\sin(x)$$ | 弧度值 | $$[-1, 1]$$ |
| `cos(x)` | 余弦 $$\cos(x)$$ | 弧度值 | $$[-1, 1]$$ |
| `tan(x)` | 正切 $$\tan(x)$$ | 弧度值 | $$(-\infty, +\infty)$$ |
| `asin(x)` | 反正弦 $$\arcsin(x)$$ | 必须在 $$[-1, 1]$$ 之间 | $$[-\pi/2, \pi/2]$$ |
| `acos(x)` | 反余弦 $$\arccos(x)$$ | 必须在 $$[-1, 1]$$ 之间 | $$[0, \pi]$$ |
| `atan2(y, x)` | 全象限反正切 | 坐标 $$(y, x)$$ | $$(-\pi, \pi]$$ |

度数转化为角度模板
```c++
#include <cmath>

const double PI = acos(-1.0);//获取pi
double degree;
double radian = degree * (PI / 180.0); // 将 度数 转换为 弧度

```

## 待办

1 同余性质等基本的数论学习（在实际比赛中往往是 部分分 到 全部分 的关键，例如AcWing 1230，不知道同余的话没办法优化到一层循环，我也是看了题解才写出来的）

2 重载运算符学习(已完成).
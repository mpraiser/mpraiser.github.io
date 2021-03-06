# 傅里叶变换及其相关
## 前言
作为一个电子专业的学生，笔者自然已经和各种花式傅立叶变换打了无数的交道。不过尽管天天用、考试用，公式背的一清二楚，但是却的确从未深究其背后的原理。在一次又一次的疑惑无法求解中笔者对写作本文的念头愈想愈烈，因此笔者终于（在一次次的咕咕咕中）下定决心整理傅立叶的各种知识以及自己的所思所想，总结成文，借由完成本文的机会，既为自己巩固知识的基础，也希望能为后人探出一条明路。

本文写作希望实现的目标有二：
1. 以==直观、平实、简单==的视角建立傅立叶变换及其相关变换的**物理图景**，帮助进行==快速、正确==的工程应用。
2. 以相对严谨的方式给予傅立叶变换及其相关变换一个==统一==的数学诠释/推导（而不是将各种变换割裂出来理解，即背公式）。

在前半文中，笔者会尽量隐去数学细节，而希望以一个完整、连贯的思路呈现整个傅立叶及其相关变换体系。在后半文中，笔者会对傅立叶体系中的一系列细节问题进行展开详细分析。

本文可能用到的一些缩写：

中文全称 | 英文缩写
:-: | :-:
（连续时间）傅里叶变换 | CTFT
离散时间傅里叶变换 | DTFT
离散傅里叶变换/级数 | DFT/DFS
快速傅里叶变换 | FFT
（连续时间）傅里叶级数 | CTFS
拉普拉斯变换 | LT
Z变换 | ZT


[TOC]

## 引入：有限维空间的向量分解与无穷维空间的函数分解
我们已经熟悉，对于三维欧几里得空间中的向量$\vec{p}$，可以在三个正交基底进行分解$\vec{p}=a\vec{x}+b\vec{y}+c\vec{z}$。其中，$a,b,c$分别是$\vec{p}$在$\vec{x},\vec{y},\vec{z}$上的投影长度。对于欧几里得空间，如果记$B$为正交基底的集合（在三维的例子下即$B=\{\vec{x},\vec{y},\vec{z}\}$），$\vec{b}$为其中一个正交基底，那么向量的正交分解可以写为：

$$\displaystyle{\vec{p}=\sum_{b\in B}\frac{\langle \vec{p},\vec{b}\rangle}{\| \vec{b}\|^2}\vec{b}}$$

现在让我们忽略掉一些数学细节，对于希尔伯特空间中的元素$x$（是一个函数）可以进行类似的操作：

$$\displaystyle{x=\sum_{b\in B}\frac{\langle x,b\rangle}{\|b\|^2}b}$$

其中$\langle f_i,f_j\rangle$为希尔伯特空间中的向量内积，计算公式如下。$\overline{f_j(x)}$是$f_j(x)$的复共轭。

$$\displaystyle{\langle f_i(x),f_j(x)\rangle =\int_{a}^{b}f_i(x)\overline{f_j(x)}}\, dx$$

这就完成了一个函数在另一组正交基底函数上的分解工作。在傅里叶变换中，基底选择的就是复指数函数$e^{j\omega t}$。
 
## （连续时间）傅里叶变换（CTFT）
函数的变换域是将一个域的函数，借由一组特定的基底函数，变换到另一个域，以便于分析和处理的方法。如在傅里叶变换中，完成的就是一个时域函数（变量为时间$t$）到频域（变量为角频率$\omega$）的变换。变换域的核心思想是使用一组特定的基底函数的组合去拟合原函数，即变换的过程就是求出各正交基底上幅度分量的过程。在傅里叶变换中，基底函数为所有频率的复指数函数$e^{j\omega t}$，$\omega \in[-\infty ,\infty]$（通常理解为正弦函数即可，链接：欧拉公式）的集合。

可以记傅里叶变换为：

$$\mathcal{F}:f(t)\rightarrow F(j\omega)$$

$$\displaystyle{
    F(j\omega)=\mathcal{F}[f(t)]=\int_{-\infty}^{\infty} f(t)e^{-j\omega t}\,dt
}$$

可以看出来傅立叶正变换的过程实际上就是求解复指数函数基底上的分量的过程，频域函数$F(j\omega)$就是复指数函数基底上的系数：

$$\displaystyle{
    F(j\omega) = \langle f(t),e^{j\omega t} \rangle
}$$

在处理完成之后，采用傅里叶逆变换（ICTFT）将信号变换回来：

$$\mathcal{F}^{-1}:F(j\omega)\rightarrow f(t)$$

$$\displaystyle{
    f(t)=\mathcal{F}^{-1}[F(j\omega)]=\frac{1}{2\pi}\int_{-\infty}^{\infty} F(j\omega)e^{j\omega t}\,d\omega
}$$

该过程具有直白的物理意义，可以理解为将各频率正弦波叠加得到原信号。

对于一种变换域方法，存在逆变换的条件的条件是基底函数**正交**，否则变换域之后各维度分量存在混叠，无法进行逆变换。

## 离散时间傅里叶变换（DTFT, Discrete Time Fourier Transformation）
对于离散时间信号，连续傅里叶变换无法直接应用。但是我们可以把离散信号看作连续信号在时间点的抽样，记抽样间隔为$T_s$，连续信号为$f(t)$，离散序列为$x(n)$，有：

$$\displaystyle{x(n)=f(t)\bigg|_{t=nT_s} \simeq f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)}$$

通过$\delta$函数的帮助（太棒了！），我们可以将连续域与离散域无割裂的联系起来。因此我们可以直接把上式带入CTFT，为了避免混淆，我们记模拟角频率为$\Omega$，数字角频率为$\omega$，$\omega=\Omega T_s$，记频域函数为$X(e^{j\omega})$，有[^1]：

[^1]: 这个方法来自《数字信号处理理论算法与实现（胡广书，第3版）》P54-55，对Z变换的推导（类比）。

$$\displaystyle{\begin{aligned}
    X(e^{j\omega}) & = \int_{-\infty}^{\infty} f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)e^{-j\Omega t}\,dt \\
    & = \sum_{n=-\infty}^{\infty}\int_{-\infty}^{\infty} f(t)e^{-j\Omega t} \delta(t-nT_s)\,dt \\
    & = \sum_{n=-\infty}^{\infty} f(nT_s)  e^{-j\Omega T_{s}n} \text{(}\delta \text{函数的性质)} \\
    \end{aligned}}$$

其中，$f(nT_s)$即为$x(n)$，又有$\omega=\Omega T_s$，则DTFT正变换公式为：

$$\displaystyle{X(e^{j\omega})=\sum_{n=-\infty}^{\infty} x(n)  e^{-j\omega n}}$$

So far so good，DTFT的问题看起来就这样轻易的解决掉了，但是在细枝末节上却隐藏着各种各样的问题。为了避免混淆思维过程，在这里先直接给出正确的IDTFT形式，[后文](#从ictft到idtft)再进行细致讨论（如果有兴趣的话）。IDTFT的正确形式为：

$$\displaystyle{
    x(n)=\frac{1}{2\pi}\int_{-\pi}^{\pi} X(e^{j\omega})e^{j\omega n}\,d\omega 
}$$

对原模拟信号的抽样过程相当于将原信号频谱进行了周期搬移，所以在一个$2\pi$周期内的频谱$X(e^{j\omega})$已经包含有原信号的全部信息了，因此只需对$[-\pi,\pi]$进行积分就可以完全还原原信号。

### 模拟角频率和数字角频率的关系
类型 | 符号| 单位
:-:|:-:|:-:
数字角频率 | $\omega$ | rad
模拟角频率 | $\Omega$ | rad/s

二者关系为$\omega=\Omega T_s$。

## 离散傅里叶变换（DFT, Discrete Fourier Transformation）
在了解了从CTFT到DTFT离散化时域的处理方法之后，我们也可以顺理成章的得到DFT了。DFT是为了计算机能进行傅里叶分析的产物。因为计算机只能处理离散量，因此虽然时域离散，但频域依然连续的DTFT不适应需求。那么怎么办？方法很简单，DTFT是通过对连续信号时域采样来得到的，那么我们只要照猫画虎对DTFT频域进行采样就可以了。假设在$\omega \in [-2\pi, 2\pi]$区间里采样$N$个点，采样后离散的频率点有：

$$\omega = \frac{2\pi}{N}k = \omega_0 k,\ k\in [0,1,2,\cdots ,N-1]$$

记$X(k)$为频域抽样得到的离散序列，即原序列$x(n)$的DFT，有（特别注意这里抽样序列有一个系数$\displaystyle{\frac{2\pi}{N}}$，是为了保证加抽样后时域信号幅值不变。具体由来在这不作展开，可以参考[从IDTFT到IDTF](#从idtft到idft)，原理是相同的（抽样序列的傅里叶变换对的系数））：

$$X(k) = X(e^{j\omega})\bigg|_{\omega = k\omega_0} \simeq X(e^{j\omega}) \cdot \frac{2\pi}{N} \sum_{k=0}^{N-1} \delta(\omega-k\omega_0)$$

对其进行IDTFT（思考一下，为什么进行的是IDTFT？），类似的可以得到：

$$\displaystyle{\begin{aligned}
    \hat{x}(n) &= \frac{1}{2\pi} \int_{-\pi}^{\pi} \frac{2\pi}{N}X(e^{j\omega})\sum_{k=0}^{N-1} \delta(\omega-k\omega_0) e^{j\omega n}\,d\omega \\
    &= \frac{1}{2\pi}\frac{2\pi}{N} \sum_{k=0}^{N-1} \int_{-\pi}^{\pi} X(e^{j\omega})\delta(\omega-k\omega_0) e^{j\omega n}\,d\omega \\
    &= \frac{1}{N} \sum_{k=0}^{N-1} X(e^{j\omega})\bigg|_{\omega = k\omega_0} e^{j\frac{2\pi}{N}nk}
\end{aligned}}$$



记$X(k) = X(e^{j\omega})\bigg|_{\omega = k\omega_0}$。另外记$W_N=e^{-j\frac{2\pi}{N}}$。由于在频域进行了抽样，反求得的$\hat{x}(n)$序列自然也是$x(n)$的周期重复，实际上我们只要取其中$n=0,1,\cdots,N-1$的点就可以了。因此先给出DFT正变换公式为：

$$\displaystyle{
    X(e^{j\omega})=\sum_{n=0}^{N-1} x(n) W_N^{nk}
}$$

IDFT公式由上述推导得出：

$$x(n)=\frac{1}{N} \sum_{k=0}^{N-1} X(k) W_N^{-nk}$$

### 离散傅里叶级数（DFS）/离散时间傅里叶级数（DTFS）
又上述讨论可以得知，DFT变换对的时域和频域其实都是无限长、周期性的。这个无穷离散周期序列到无穷离散周期序列的变换称作离散傅里叶级数（DFS，证明略）。他本质上与DFT是一致的，区别在于DFT是只取其中$N$点进行变换。

### 快速傅里叶变换（FFT, Fast Fourier Transformation）
DFT有被非常广泛应用的快速算法，即FFT。

## 再谈（连续时间）傅里叶级数（CTFS）
笔者在写下这份笔记的时候，其实是非常不愿意讨论傅里叶级数的，因为他形式繁琐，又臭又长，让数学苦手的笔者十分头大，而且在实际分析中很少用得上。奈何查阅的绝大多数资料都是从傅里叶级数讲起，逐步推导出傅里叶变换。这对于理解和应用来说既繁琐又不必要。但是，在从CTFT到DTFT，再到DFT的推进过程中，CTFS也逐渐揭示出清晰的属于他的位置。正如DFT实质上也是DTFS，CTFS实质上为**连续时域**到**离散频域**的变换。

推导方式多种多样 殊途同归

变换类型 | 时域性质 | 频域性质
:-:|:-:|:-:
CTFT | 连续、非周期 | 连续、非周期
CTFS | 连续、周期 | 离散、非周期
DTFT | 离散、非周期 | 连续、周期
DFT | 离散、周期 |离散、周期

## 拉普拉斯变换（LT, Laplace Transformation）
LT可以看作是FT的一个推广；或者说，FT是LT的一个特例，他们的区别在于基函数不同。FT的基函数是复指数函数$e^{j\omega t}$，而LT的是带常数的复指数函数$e^{\sigma+j\omega t}$，这使得LT相比于LT具有更强的分析能力，同时保持了极高的相似性。有：

$$\displaystyle{
    F(s) = \mathcal{L}[f(t)] = \int_{0}^{+\infty} f(t)e^{st} \, dt
}$$

$$s=\sigma + j\omega$$

 
## Z变换
采用从CTFT推导DTFT同样的方法我们可以简单的从Laplace变换得到Z变换：

### 从Z变换得到DTFT

## Gibbs现象

## 截断误差、频谱泄漏

# 一些数学
## 反变换的系数？
进行函数分解基底需要满足一定的条件，首先是**正交**，但最好是**规范正交**（即不仅相互正交，而且各自都是单位向量）。若$\{f_0(x),f_1(x),f_2(x),\cdots\}$为一组基底，各基底是取值在$[a,b]$上的函数，要使其规范正交，需要满足：

$$ 
\left\{
\begin{aligned}
\langle f_i,f_j\rangle=0 &, & i\not= j \\
\langle f_i,f_i\rangle=1
\end{aligned}
\right.
$$

对于傅里叶变换的正交基底复指数函数$e^{j\omega t}$，为了满足规范正交的条件，设基底系数为$k$，必须满足：

$$\displaystyle{\int_{-\pi}^{\pi}k^2e^{j(\omega_0-\omega_0 )t}\,dt=1}$$

$$\displaystyle{\int_{-\pi}^{\pi}k^2\,dt=1}$$

$$\displaystyle{k=\frac{1}{\sqrt{2\pi}}}$$

即是说正交基底为$\left \{ \cdots, \dfrac{e^{-2j\omega t}}{\sqrt{2\pi}}, \dfrac{-e^{j\omega t}}{\sqrt{2\pi}}, \dfrac{1}{\sqrt{2\pi}}, \dfrac{e^{j\omega t}}{\sqrt{2\pi}}, \dfrac{e^{2j\omega t}}{\sqrt{2\pi}}, \cdots \right \}$（实际上$\omega$取值是连续的，不然就变成傅里叶级数了）。如同向量的正交分解，各基底上的分量通过$\langle f(t),\dfrac{e^{kj\omega t}}{\sqrt{2\pi}}\rangle$求出。

如果使用这组正交基底的话，傅里叶变换对就可以写为：

$$\displaystyle{F(j\omega)=\int_{-\infty}^{\infty} f(t)\frac{e^{-j\omega t}}{\sqrt{2\pi}}\,dt}$$

$$\displaystyle{f(t)=\int_{-\infty}^{\infty} F(j\omega)\frac{e^{j\omega t}}{\sqrt{2\pi}}\,d\omega}$$

这样就消去了原公式中逆变换的系数$\dfrac{1}{2\pi}$，更加优雅和谐。如要得到原公式,将上式略作变形：

$$
\displaystyle{
\begin{aligned}
f(t) &= \int_{-\infty}^{\infty} \int_{-\infty}^{\infty} f(t)\frac{e^{-j\omega t}}{\sqrt{2\pi}}\,dt\frac{e^{j\omega t}}{\sqrt{2\pi}}\,d\omega \\
&=\frac{1}{\sqrt{2\pi}}\frac{1}{\sqrt{2\pi}}\int_{-\infty}^{\infty} \int_{-\infty}^{\infty} f(t){e^{-j\omega t}}\,dte^{j\omega t}\,d\omega \\
&=\frac{1}{2\pi}\int_{-\infty}^{\infty} F(j\omega)e^{j\omega t}\,d\omega
\end{aligned}}$$


即可得到原式的傅里叶反变换，其中：

$$\displaystyle{F(j\omega)=\int_{-\infty}^{\infty} f(t){e^{-j\omega t}}\,dt}$$

为对应的傅里叶正变换。系数的提取仅相当于对得到的频域图形等比例缩放，在反变换时加上去即可，不会影响单独时域或者频域的分析。习惯上来说（由于历史原因），提取系数（即逆变换带系数$\dfrac{1}{2\pi}$）的傅里叶变换公式更为常见。喜欢使用带$\dfrac{1}{2\pi}$的原因可能是因为对计算机计算更友好（只需要在正变换加入$\pi$的相关计算了）。

对于IDTFT和IDFT的系数，请参考下文[从ICTFT到IDTFT](#从ictft到idtft)，[从IDTFT到IDFT](从idtft到idft)。

## 帕塞瓦尔定理（Parseval's Theorem）
如同线性空间中一个向量在不同基底表示下长度（范数）依然相同一样，傅里叶变换前后函数的能量（范数）相同。这个定理对我们来说最直观的物理意义是：在时域求信号能量，与在频域求信号能量一致。它通常可以表述为：

$$\displaystyle{
    \int_{-\infty}^{\infty} |f(t)|^2 \, dt = \frac{1}{2\pi} \int_{-\infty}^{\infty} |F(j\omega)|^2 \, d\omega
}$$

这个$\dfrac{1}{2\pi}$系数由来和ICTFT中的系数由来原因相同，因为相比于使用规范正交基的CTFT，常见的CTFT公式中得到的频域函数$F(j\omega)$幅值扩大了$\sqrt{2\pi}$倍，为了保证相等需要额外加上校正系数$\dfrac{1}{2\pi}$。当然，如果CTFT采用[上文](#反变换的系数)中使用的规范正交基，这个系数就没有了，帕塞瓦尔定理形式简化为：

$$\displaystyle{
    \int_{-\infty}^{\infty} |f(t)|^2 \, dt = \int_{-\infty}^{\infty} |F(j\omega)|^2 \, d\omega
}$$

更加优雅。实际上含$\dfrac{1}{2\pi}$系数的帕塞瓦尔定理公式是数学上不严谨的，因为严格意义上的帕塞瓦尔定理本身为基底规范正交的等价条件[^2]。可以直观理解为毕达哥拉斯定理（勾股定理）的推广（正交的各边平方和等于最长边平方），没有任何多余的系数，非常漂亮：


[^2]: 《泛函分析基础》刘培德著。P174-175，Hilbert空间几何学 定理4。

$$\displaystyle{
    \|x\|^2 = \sum_{n=1}^{\infty} |\langle x,e_n \rangle|^2
}$$

有的文章认为带$\dfrac{1}{2\pi}$系数的帕塞瓦尔定理从频域（$f$域，单位$Hz$，而非角频率$\omega$）理解可以得到本质，这个观点我是不同意的。我认为从两个“形式无关紧要”中得出一个“本质必然”着实没有道理，只不过是把巧合误当作了真理。

$$\displaystyle{\begin{aligned}
    \int_{-\infty}^{\infty} |f(t)|^2 \, dt & = \frac{1}{2\pi} \int_{-\infty}^{\infty} |F(j\omega)|^2 \, d\omega \\
    & = \int_{-\infty}^{\infty} |F(j2\pi f)|^2 \, d\frac{2\pi f}{2\pi} \\
    & = \int_{-\infty}^{\infty} |F(j2\pi f)|^2 \, df
\end{aligned}}$$

对于DTFT和DFT同样可以给出适应其形式的帕塞瓦尔定理，对DTFT有：

$$\displaystyle{
    \sum_{n=-\infty}^{\infty} |x(n)|^2 = \frac{1}{2\pi} \int_{-\infty}^{\infty} |X(e^{j\omega})|^2 \, dw
}$$

对DFT有：

$$\displaystyle{
    \sum_{n=0}^{N-1} |x(n)|^2 \, dt = \frac{1}{N} \sum_{k=0}^{N-1} |X(k)|^2
}$$

## 从ICTFT到IDTFT
使用相同的思路（从CTFT导出DTFT）来考虑IDTFT，直接带入$X(e^{j\omega})$，有：

$$\displaystyle{\begin{aligned}
    & f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s) \\
    &=\frac{1}{2\pi}\int_{-\infty}^{\infty} X(e^{j\omega})e^{j\Omega nT_s}\,d\Omega \\
    &=\frac{1}{T_s}\frac{1}{2\pi}\int_{-\infty}^{\infty} X(e^{j\omega})e^{j\omega n}\,d\omega 
\end{aligned}}$$

可以观察到，该式与正确的IDTFT形式具有区别。区别有二，一是在积分区间，二是在系数。我们在上文提到DTFT频谱中在一个$2\pi$区间中，已经具备了原信号完全的信息，这个结论显然是正确的。那么为什么会产生这种看似矛盾的佯谬呢？

原因在于对模拟信号的抽样这一步：

$$\displaystyle{x(n)=f(t)\bigg|_{t=nT_s} \simeq f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)}$$

可以看到上文中在写抽样这一步时，没有写“$=$”，而是写了“$\simeq$”，原因在于，这两者是有细微差异的。回顾$\delta$函数的定义：

$$\left\{\begin{aligned}
    & \delta (t)=0, x\not=0\\
    & \int_{-\infty}^{\infty} \delta (t)\, dt=1
\end{aligned}\right.$$

可见$\delta$函数在非0的位置均为0，在0的位置是一个无穷大（尽管不能简单的把它当成无穷大，但我们暂且这么表示。后文同理）。这就带来了一个问题：当使用$\delta(t-nT_s)$对$f(t)$进行抽样的时候，在$t=nT_s$处得到的并非是$f(nT_s)$的值，而是以$f(nT_s)$为系数的一个无穷大，只有当积分之后才是$f(nT_s)$本身。因此，上文的推导在带入CTFT时，实际带入的是一串带系数的冲激序列。但是最终得到的公式中的$x(n)$确实是系数的序列，仅包含$\{f(nT_s)\}$本身。这有无矛盾？其实并没有。$x(n)$本身就是对各点抽样值的一个表示，而抽样信号$\delta(t-nT_s)$被隐含了。在做离散信号分析的时候，我们关心的是离散序列本身，至于冲激我们并不关心，只需要对$x(n)$作处理就好了，因此DTFT才脱胎于CTFT。

这给我们探讨IDTFT带来了一点点小麻烦，因为套用ICTFT得到的必然是带系数的冲激序列$f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)$（而我们只想求得$x(n)$），对$[-\infty,\infty]$的积分也确实会导致一个无穷大的冲激。剩下的问题只有系数$\frac{1}{T_s}$。这个系数的由来可以这么解释：

首先我们回到$f(t)$，$f(t)$的频谱为$F(j\Omega)$。不妨记抽样序列$\displaystyle{\hat{f}(t)=f(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)}$，有$\hat{f}(t)$的频谱$\hat{F}(j\Omega)=X(e^{j\omega})$。$F(j\Omega)$与$\hat{F}(j\Omega)$之间有什么关系（当然，假设前提是抽样频率足够大。链接：Nyquist采样定理）？在这里我们先引用一傅里叶变换对：

$$\displaystyle{
    \sum_{n=-\infty}^{\infty}\delta(t-nT_s) \rightleftharpoons \Omega_s \sum_{n=-\infty}^{\infty}\delta(\Omega-n\Omega_s)
    }$$

$$\displaystyle{
    \Omega_s = 2\pi f_s=\frac{2\pi}{T_s}
    }$$

由CTFT的时域乘法性质，频域相互卷积，有：

$$\displaystyle{
    \begin{aligned}
    \hat{F}(j\Omega) & = \frac{1}{2\pi}F(j\Omega) * \frac{2\pi}{T_s}\sum_{n=-\infty}^{\infty}\delta(\Omega-n\Omega_s) \\
    & = \frac{1}{T_s}\sum_{n=-\infty}^{\infty}F(j(\Omega-n\Omega_s))
    \end{aligned}}$$

说明$\hat{F}(j\Omega)$是$F(j\Omega)$的周期重复，且幅值下降为$\displaystyle{\frac{1}{T_s}}$，且频谱不混叠的前提下，一个$\Omega \in [-\Omega_s, \Omega_s]$的单个周期内即含有原信号的全频谱。根据数字角频率和模拟角频率的关系$\omega = \Omega T_s$，即在$\omega \in [-2\pi, 2\pi]$的区间即为$f(t)$的全频谱。因此要得到$f(t)$本身，只需对$\hat{F}(j\Omega)$乘以系数$T_s$，在$\omega \in [-2\pi, 2\pi]$进行ICTFT就可以得到$f(t)$。该过程可以写为：

$$\displaystyle{\begin{aligned}
    f(t) &= \frac{1}{2\pi}\int_{-\infty}^{\infty} F(j\Omega)e^{j\Omega t}\,d\Omega\\
    &= \frac{1}{2\pi}\int_{-\infty}^{\infty} \frac{1}{T_s} F(j\Omega)e^{j\Omega t}\,d\omega \\
    &= \frac{1}{2\pi} \int_{-2\pi}^{2\pi} \hat{F}(j\Omega) e^{j\Omega t} \, dw
\end{aligned}}$$

此时你可能会突然迷惑了：绕了这么一大圈，我们的目标究竟是什么？但仔细理一理，可以发现方向并没有歪。我们的最终目标是**通过反变换求得$x(n)$**，而$x(n)$是不含无穷大的抽样冲激的那一个。因此（实质上是）通过ICTFT从$x(n)$的频域函数$X(e^{j\omega})$反求得$f(t)$，再通过$x(n)=f(t)\bigg|_{t=nT_s}$，选取$f(t)$在$t=nT_s$点上的函数值。同时$\hat{F}(j\Omega)$实质上就是$X(e^{j\omega})$，至此，我们终于得到正确形式的IDTFT：

$$\displaystyle{\begin{aligned}
    f(t)\bigg|_{t=nT_s} &= \frac{1}{2\pi} \int_{-2\pi}^{2\pi} \hat{F}(j\Omega) e^{j\Omega t} \, dw \bigg|_{t=nT_s} \\
    &= \frac{1}{2\pi} \int_{-2\pi}^{2\pi} X(e^{j\omega}) e^{j\Omega nT_s} \, dw \\
    &= \frac{1}{2\pi} \int_{-2\pi}^{2\pi} X(e^{j\omega}) e^{j\omega n} \, dw \\
\end{aligned}}$$

## 从IDTFT到IDFT
关于DFT中所加采样函数的系数问题，回想从ICTFT到IDTFT中使用的变换对：
$$\displaystyle{
    \sum_{n=-\infty}^{\infty}\delta(t-nT_s) \rightleftharpoons \Omega_s \sum_{n=-\infty}^{\infty}\delta(\Omega-n\Omega_s)
}$$
在从DTFT到DFT中，进行的是类似的操作，不过因为是对频域进行采样，所以频域的采样函数需要补上上方变换对的系数。

$$\displaystyle{
    \sum_{n=-\infty}^{\infty}\delta(t-nT_0) \rightleftharpoons \omega_0 \sum_{k=-\infty}^{\infty} \delta(\omega-k\omega_0)
}$$

又有：

$$\omega_0 = \frac{2\pi}{N}$$

$$T_0 = N$$

## 频域函数自变量的问题
在CTFT中，频域函数通常写成$F(j\omega)$。

在DTFT中，频域函数通常写成$X(e^{j\omega})$。

但是在分析频谱的时候，通常都是以$\omega$为横坐标轴进行绘图。

我也不知道为什么，我目前知道的唯一有说服力的理由是为了**区别CTFT、DTFT、DFT的频域函数**。

## 从FT到LT
Dirichlet条件和Gibbs现象


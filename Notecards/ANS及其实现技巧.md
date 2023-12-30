
ANS (Asymmetric Numeral Systems) 是一种压缩手段，这里会推导其实现的部分数学证明，以及实现上的一些优化技巧。
本文参考了 ANS 论文原文：[[1311.2540] Asymmetric numeral systems: entropy coding combining speed of Huffman coding with compression rate of arithmetic coding]( https://arxiv.org/abs/1311.2540 ) 以及一篇博客：
[Lossless Compression with Asymmetric Numeral Systems | Bounded Rationality](https://bjlkeng.io/posts/lossless-compression-with-asymmetric-numeral-systems/)

## 从二进制转换谈起
对于一个二进制串 $b_1b_2...b_i$，我们很容易将它变成一个十进制数字，反之亦然。这其实可以看成一个编码解码的过程，自然的，编码解码公式如下：
$$
\begin{align*}
C(x_i,b_{i+1}) &:= 2x_i+b_{i+1}\\
x_i,b_{i+1} &:=D(x_{i+1})=(\lfloor \frac{x_{i+1}}{2}\rfloor, x_{i+1} \mathrm{~mod~}2)
\end{align*} \tag{1}
$$
但是，对于二进制串 00,000... 上面的方法无法区分，但只需要令 $x_0=1$ 即可。
如果假设二进制串中 0 和 1 出现的概率相等，上面的编码手段是最优的，因为：
$$
H(x)=-\frac{1}{2}\log\frac{1}{2}\times 2=1 \tag{2}
$$
上面的编码方法的也是以 1 bit 来进行编码。

## 概率不等的情况
假设二进制串中 1 出现的概率 $p_1=p<1-p=p_0$，那么显然上面的编码手段不是最优的，那么该如何取寻找最优（近似）的编码手段呢？
假设编码的形式和上面的一样，并假设有一个最优编码器：$x_{i+1}=C_{opt}(x_i,b_i+1)$，并假设对一个数 $x_i$ 有 $\log_{2}x_{i}$ bit 的信息，以及最优的情况下，以 $-\log_{2}p_{b_{i+1}}$ bit 来编码 $b_{i+1}$ ，那么可以作如下推导：
$$\begin{aligned}
H(x_{i+1})& =H(C_{\mathrm{opt}}(x_i,b_{i+1}))  \\
&=H(x_i)+H(b_{i+1}) \\
&=\log_2(x_i)-\log_2(p_{b_{i+1}}) \\
&=\log_2(\frac{x_i}{p_{b_{i+1}}}) \\
&\Longrightarrow C_{\mathrm{opt}}(x_i,b_{i+1})\approx\frac{x_i}{p_{b_{i+1}}}
\end{aligned} \tag{3}$$

因此，我们的编码器应该尽量满足上面的形式。其实上面的这个式子完全可以看成一个概率似的问题。

下面，我们换一种视角来看这个问题。我们可以认为上面的编码是一种映射 $\mathbb R \rightarrow \{0,1\}$。$x_{i+1}= C(x_i,b_{i+1})$ 将 $x_{i+1}$ 映射为 $b_{i+1}$  , $x_{i+1}$ 就是第 $x_i+1$ 个 $b_{i+1}$ 出现的位置（也就是 $x_{i+1}$ 的前面有 $x_i$ 个 $b_{i+1}$），我们可以研究下 1 和 0 的分布情况，以 1 来看，由于 $x_i$ 是连续的自然数，那么数轴上 1 的间隔接近于 $\frac{1}{p}$。这样的话，从统计的观点看，对于 $N$ 个数字，其中映射到 1 的个数约是 $Np$，映射到 0 的个数约是 $N(1-p)$。
先进行定义：
- $f(x):\mathbb R \rightarrow \{0,1\}$，是映射函数。
- $x^s:=|\{y\in\{0,1,..,x-1\}:f(y)=s\}|$，表示前 $x$ 个数中符号 $s$ 出现的个数。

现在，可以假设：
$$
\begin{aligned}x^1&:=\lceil xp\rceil\\\\x^0&=x-x^1=x-\lceil xp\rceil\end{aligned}
$$
在这样的假设下：
$$
f(x)=\lceil(x+1)\cdot p\rceil-\lceil x\cdot p\rceil
$$
这样的假设很符合我们的需求，它带来了两个好处：
1. 前 $N$ 个数中符号 $s$ 出现的次数约等于 $Np_{s}$.
2. 从 $f(x)$ 来看，它将 1 放在 $Np$ 的整数部分即将发生变化的地方，也就是说，每两个 1 的间隔接近 $\frac{1}{p}$。

那这种关系该怎么体现在编码函数上呢？现在给出说明：

设 $r=\lceil xq\rceil - xq,r \in[0,1)$。有：
$$
\begin{align*}
f(x) &= \lceil(x+1)\cdot p\rceil-\lceil x\cdot p\rceil\\
&= \lceil (x+1)p - \lceil xp \rceil \rceil\\
&= \lceil (x+1)p - r - xp \rceil\\
&= \lceil p-r \rceil
\end{align*}
$$
- 如果 $f(x) = 1$，则有 $0 \leq r < p$。那么有：
$$
\begin{align*}
x^{1}&= \lceil xp \rceil\\
&= xp + r\\
\Longrightarrow x&= \frac{x^{1}-r}{p}
\end{align*} 
$$
由于 $x$ 是整数，那就得到 $x=\lfloor \frac{x^{1}}{p}\rfloor$。
- 如果 $f(x)=0$, 则 $p\leq r<1$,所以 $\tilde{p}=1-p\geq 1-r>0$，
$$
\begin{align*}
x^0&=x-\lceil xp\rceil=x-xp-r=x\tilde{p}-r\\
x&=\frac{x^0+r}{\tilde{p}}=\frac{x^0+1}{\tilde{p}}-\frac{1-r}{\tilde{p}}=\left\lceil\frac{x^0+1}{\tilde{p}}\right\rceil-1
\end{align*}
$$

下面我们来看一下这意味着什么，对于 $x_{i+1}=C(x_i,b_{i+1})$，是指第 $x_i+1$  次出现的 $b_{i+1}$ 的位置是 $x_{i+1}$，也就是说 $x_i=x_{i+1}^{b_{i+1}}$,那么就得到：
$$
C(x_i,b_{i+1})=
\begin{cases}
\lceil\frac{x_i+1}{1-p}\rceil-1 \quad &\mathrm{~if~}b_{i+1}=0  \\
\lfloor\frac{x_i}p\rfloor\quad&\mathrm{~if~}b_{i+1}=1
\end{cases}
$$

对于解码函数 $(x_i,b_{i+1}) =D(x_{i+1})$ ，首先，$b_{i+1}=\lceil(x_{i+1}+1)\cdot p\rceil-\lceil x_{i+1}\cdot p\rceil$, 其次，由 $x_i=x_{i+1}^{b_{i+1}}$，以及所作的假设，很容易得到 $x_i$ 的表达式，解码函数如下：
$$\begin{aligned}
(x_i,b_{i+1})& =D(x_{i+1})  \\
b_{i+1}& =\lceil(x_{i+1}+1)\cdot p\rceil-\lceil x_{i+1}\cdot p\rceil   \\
x_{i}& \left.=\left\{\begin{aligned}x_{i+1}-\lceil x_{i+1}\cdot p\rceil\quad&&\mathrm{if~}b_{i+1}=0\\\lceil x_{i+1}\cdot p\rceil\quad&&\mathrm{if~b_{i+1}=1}\end{aligned}\right.\right. 
\end{aligned}$$

## rANS
### 函数推导
uANS 处理的符号表只有两位，其实很简单就能扩展到符号表有多个的情况，依旧只需要抓住两个要点：
- $C(x_{i},b_{i+1})$ 得到的值将被映射成 $b_{i+1}$，并且该数的前面有 $x_i$ 个 $b_{i+1}$，$(x_i,b_{i+1})=D(x_{i+1})$ 则是逆过程。
- 数轴上的映射概率和符号本身的概率接近，这样的话，才可能满足 $C(x_i,b_{i+1})\approx\frac{x_i}{p_{b_{i+1}}}$。
：
定义符号表 $\mathcal{A}=\{0,..,n-1\}$，$\{p_s\}_{s\in\mathcal{A}}，\sum_sp_s=1$.
对于多个符号，涉及到精度问题，定义精度尺度 $2^n$，为每个 $s\in \mathcal A$，有 $\frac{f_{s}}{2^{n}} \approx p_{s}$，并且 $\sum_{s}f_{s}=2^n$。在编码之前，获得了 $p_s$ 后，将其转换为 $f_s$，需要注意的是，不能有某个 $s$，其 $f_s=0$。接着定义 $CDF(s)=\sum_{0}^{s-1}f_s$。

现在要考虑如何在数轴上排列这些符号，rANS 比 uANS 的方法还要简洁一些：将自然数分为块，每块 $2^n$ 长，每一块上按照 $f_s$ 连续地排列符号。（注意数轴是从 0 开始）

这样的话，很容易得到编码函数：
$$
\begin{align*}
C(x_i,s_{i+1})& =\lfloor \frac{x_{i}}{f_{s_{i+1}}} \rfloor 2^{n} -1+ CDF(s_{i+1})+x_{i} \mathrm{~mod~}f_{s_{i+1}}+1\\
& =\lfloor \frac{x_{i}}{f_{s_{i+1}}} \rfloor 2^{n} + CDF(s_{i+1})+x_{i} \mathrm{~mod~}f_{s_{i+1}}
\end{align*}
$$
- 先算第 $x_i$ 个 $s_{i+1}$ 的位置，那么先计算它跨越的块数，得到：$\lfloor \frac{x_{i}}{f_{s_{i+1}}} \rfloor 2^{n}-1$，这就到了最后一个块的开始位置。
- $CDF(s_{i+1})+x_{i} \mathrm{~mod~}f_{s_{i+1}}$ 计算在最后一个块中的位移。
- 最后 $+1$，得到第 $x_{i}+1$ 个 $s_{i+1}$ 的位置。之所以能够直接加一，是因为 $x_{i}\mathrm{~mod~}f_{s_{i+1}}\in [0,f_{s_i+1})$，第 $x_{i}+1$ 个 $s_{i+1}$ 只会出现在这个块中，而不是下一个或前一个块里。  
解码函数也同样很容易得到：
由于数排列的性质，有 $f(x)=f(x+k2^n)$，也就是有 $f(x)=f(x\mathrm{~mod~}2^n)$。 $f(x)$ 在 $[0,2^{n}-1)$ 的表达式是：
$$
\begin{align*}
s=f(x)~x\in [0,2^{n}-1)\\
where ~~CDF(s)\leq x< CDF(s+1)
\end{align*}
$$

- 在第一个一个块中（$[0,2^{n}-1)$），符号 $s$ 占据区域是 $[CDF(s),CDF(s+1)$,这也就是上面的函数的由来。
解码函数，得到 $x_{i+1}$ 被映射成的符号，和得到 $x_{i+1}$ 前面的数（从 $0$ 到 $x_{i+1}-1$ 共 $x_{i+1}$ 个数）中有几个 $s_{i+1}$，则解码函数如下：

$$
\begin{align*}\\
(x_i,s_{i+1})&=D(x_{i+1})\\

s_{i+1}&=f(x_{i+1}\mathrm{~mod~}2^{n})\\
x_{i}&= \lfloor \frac{x_{i+1}}{2^ {n}}\rfloor f_{s_{i+1}}+x_{i+1}\mathrm{~mod~}2^{n}-CDF(s_{i+1})
\end{align*}
$$

- 前面共有 $x_{i+1}$ 个数，则 $\lfloor \frac{x_{i+1}}{2^ {n}}\rfloor f_{s_{i+1}}$ 找到一共循环了几次，每一次循环就有 $f_{s_{i+1}}$ 个数。
- 由于数 $x_{i+1}$ 本身是被映射到了 $s_{i+1}$，那么它的前一个数 $x_{i+1}-1$ 在块中的位置（第几个数） $x_{i+1}\mathrm{~mod~}2^n$ 是大于等于 $CDF(s_{i+1})$ 的，这个减法算的是最后一块中剩余的 $b_{i+1}$ 个数。

### 注意事项

和 uANS 中一样，开头的 $x_0$ 不能取 0，适当的取一个大一点的数。

另外如果文件很大，编码得到的数字成指数级增长，计算机无法表示，这里就需要用到一个技巧。
我们看编码函数：$C(x_i,b_{i+1})=x_{i+1}$，它随着符号流的输入一直连续运行，累计的 $x$ 的值指数增长，但其实，数 $x$ 本身是没有意义的，而符号 $b$ 才是有意义的，在运行过程中，可以对 $x$ 进行一些限制，只要不影响解码过程中找到对应的 $x$ 就可以了。

可以将 $x_i$ 限制在 $x_i\in[2^M,2^{2M}-1].$，其中 $M>n$。为了达到这一点，首先，其起始的 $x_0$ 应该大，取 $2^M-1$ 或其它更大的数。
如果发现某个 $x_{i+1}=C(x_{i},s)\geq 2^{2M}$，那么 $x_i$ 的低 $M$ 位写入文件中，右移 $M$ 位，用这个新的数 $x_i^h$ 来继续进行。
那么 $x_{i}^{h}<2^{M}$。编码得到 $x_{i+1}=C(x_{i}^{h},s)$。
那么解码端在解码 $x_{i+1}$ 时，得到 $x_{i}^{h}$ 发现它小于 $2^M$，就会读取 16 位来构成完整的 $x_i$。

但这个过程是有问题的，就是需要证明 $x_{i+1}=C(x_{i}^{h},s)\geq 2^M$，否则，在解码 $x_{i+2}$ 得到 $x_{i+1}$ 后，会错误的读取 16 位 bit。
下面来证明：
$$
\begin{align*}
has~C(x_{i},s)&=\lfloor \frac{x_{i}}{f_s} \rfloor 2^{n} + CDF(s)+x_{i} \mathrm{~mod~}f_{s}\geq2^{2M}\\
x_{i}^{h}&= \lfloor \frac{x_{i}}{2^{M}} \rfloor\\
to prove~\lfloor \frac{\lfloor \frac{x_{i}}{2^{M}}\rfloor}{f_{s}} \rfloor 2^{n}&+CDF(s)+\lfloor \frac{x_{i}}{2^{M}} \rfloor \mathrm{~mod~}f_{s} \geq 2^M
\end{align*}
$$

首先，对应整数 $a,b,c$ 有 $\lfloor\frac{\lfloor \frac{a}{b} \rfloor} {c}\rfloor=\lfloor \frac{\frac{a}{b}}{c} \rfloor=\lfloor \frac{a}{bc} \rfloor$,

尝试了一下，没证出来... 但后面有一种构造性的证明。

但其实这个范围本身是有问题的，范围不必限制这么死，很灵活。

## 开源库中的实现技巧
选择学习的开源库是 [GitHub - rygorous/ryg\_rans: Simple rANS encoder/decoder (arithmetic coding-ish entropy coder).](https://github.com/rygorous/ryg_rans)，因为 compressAI 中使用的是这个库。下面会解析 rans64. h 的实现。

### renormalize

它选择令 $x_i$ 的范围在 $[2^{31},2^{63})$，后面会看到这个选择的巨大好处。
```cpp
#define RANS64_L (1ull << 31)  // lower bound of our normalization interval
static inline void Rans64EncPut(Rans64State* r, uint32_t** pptr, uint32_t start, uint32_t freq, uint32_t scale_bits)
{
    Rans64Assert(freq != 0);
    // renormalize (never needs to loop)
    uint64_t x = *r;
    uint64_t x_max = ((RANS64_L >> scale_bits) << 32) * freq; // this turns into a shift.
    if (x >= x_max) {
        *pptr -= 1;
        **pptr = (uint32_t) x;
        x >>= 32;
        Rans64Assert(x < x_max);
    }
    // x = C(s,x)
    *r = ((x / freq) << scale_bits) + (x % freq) + start;
}
```

`x_max = ((RANS64_L >> scale_bits) << 32) * freq`，也就是 $x_{max}=\frac{2^{low+32}}{2^{n}}f$。
有 $C(x_{max},s)= 2^{low+32}+t,0\leq t <2^n$，其中 $low+32=63$ 也就是上界，如果 $x=x_{max}-1$，那么前面的一项 $\lfloor{\frac{x}{f}}\rfloor$
会直接减少 1 (因为 $x_{max}$ 时这一项刚好是整数)，那么 $C(x,s)<2^{low+32}$。所以 $x_{max}$ 自然就成为了分界线，这样能够减少运算。
现在，同样的，需要证明新的 $x^h=\lfloor{\frac{x}{2^{32}}}\rfloor$ 编码得到的数大于等于 $2^{low}$。
$$
\begin{align*}
x& \geq x_{max}=\frac{2^{low+32}}{2^n}f\\
\rightarrow x^{h}&\geq \frac{2_{low}}{2^{n}}f\\
\rightarrow C(x^{h},s)&=\lfloor{\frac{x^{h}}{f}}\rfloor2^{n}+ CDF(s)+x^{h}\mathrm{~mod~}f\\
&\geq 2^{low}
\end{align*}
$$
另外，这个 $C(x^{h},s)$ 也不可能超过上界，否则意味着 $x^{h}\geq x_{max}$，而这是不可能的，因为：
$$
\begin{align*}
if~~x^{h}&\geq x_{max}\\
\rightarrow x&\geq2^{32}x_{max}\\
&= 2^{low+32} \frac{2^{32}}{2^{n}}f\\
&>2^{low+32}\\
but~~x &\in[2^{low},2^{low+32})
\end{align*}
$$
得证，同样前面地方的证明可以用同样的方法得到。

解码的代码如下：
```cpp
static inline void Rans64DecAdvance(Rans64State* r, uint32_t** pptr, uint32_t start, uint32_t freq, uint32_t scale_bits)
{
    uint64_t mask = (1ull << scale_bits) - 1;
    // s, x = D(x)
    uint64_t x = *r;
    x = freq * (x >> scale_bits) + (x & mask) - start;
    // renormalize
    if (x < RANS64_L) {
        x = (x << 32) | **pptr;
        *pptr += 1;
        Rans64Assert(x >= RANS64_L);
    }
    *r = x;
}
```

### 除法加速
可以查看这里：[[无符号整数除法计算的优化|除法计算的优化]]

在此项目中，由于最大长度 $L=63$，因此 $m$ 的范围是 $[2^{63},2^{64}]$，还是可以用 `uint_64` 来表示。

```cpp
    // The original encoder does:
    //   x_new = (x/freq)*M + start + (x%freq)
    //
    // The fast encoder does (schematically):
    //   q     = mul_hi(x, rcp_freq) >> rcp_shift   (division)
    //   r     = x - q*freq                         (remainder)
    //   x_new = q*M + bias + r                     (new x)
    // plugging in r into x_new yields:
    //   x_new = bias + x + q*(M - freq)
    //        =: bias + x + q*cmpl_freq             (*)
```

这里 `start` 对应着 $CDF(s)$，对于确定的符号来说是个常数。上面将除法 $\lfloor{\frac{x}{f}}\rfloor$ 进行了优化。并重写了编码表达式。

$\lfloor{\frac{x}{f}}\rfloor$ 优化成了 $\lfloor{\frac{m\cdot x}{2^{k}}}\rfloor$,其中 $k=L+\lceil{\log_2^f}\rceil$。
$$
\begin{align*}
\lfloor{\frac{m\cdot x}{2^{k}}}\rfloor &= \lfloor{\big(\frac{m\cdot x}{2^{64}}}\big)>>(\lceil{\log_2^f}\rceil-1)\rfloor\\
&= \lfloor{\lfloor\frac{m\cdot x}{2^{64}}}\rfloor>>(\lceil{\log_2^f}\rceil-1)\rfloor.
\end{align*}
$$
$\frac{m\cdot x}{2^{64}}$ 可以使用
```cpp
static inline uint64_t Rans64MulHi(uint64_t a, uint64_t b)
{
    return (uint64_t) (((unsigned __int128)a * b) >> 64);
}
```
来实现。

对于 $m$ 的计算，也就是 `rcp_freq`，等于 $\lceil\frac{2^{k}}{f}\rceil$，也就是使用 `((uint128) (1 << (shift + 63)) + freq-1) / freq` 来计算。(值得注意的是，$freq$ 是 32 位的。) 项目中使用一些技巧来加速这个计算：简写 $s:shift,f:freq$
$$
\begin{align*}

let~~2^{s+31} &= tf + e ~~where~0\leq e<f\\
so~~\lfloor\frac{2^{s+63}+f-1}{f}\rfloor&= \lfloor\frac{2^{s+31+32}+f-1}{f}\rfloor\\
&=\lfloor \frac{2^{32}(tf+e) + f-1}{f}\rfloor\\
&=\lfloor2^{32}t+\frac{2^{32}e+f-1}{f}\rfloor\\
&= 2^{32}t + \lfloor{\frac{2^{32}e+f-1}{f}}\rfloor
\end{align*}
$$
项目中的代码如下：
```cpp
        x0 = freq - 1;
        x1 = 1ull << (shift + 31);
        t1 = x1 / freq;
        x0 += (x1 % freq) << 32;
        t0 = x0 / freq;
        s->rcp_freq = t0 + (t1 << 32);
```


我们的除法加速有一个需要注意的特例，那就是当 $f=1$ 的时候，$\lceil{\log_2^f}\rceil-1=-1$，那上面的过程就出错了。此时，作者做出了别的尝试，直接令此时 $rcp\_freq=2^{64}-1,rcp\_shift=0$ ，那么
```cpp
        //   q = mul_hi(x, rcp_freq) >> rcp_shift
        //     = mul_hi(x, (1<<64) - 1)) >> 0
        //     = floor(x - x/(2^64))
        //     = x - 1 if 1 <= x < 2^64
```

由设定的阈值，$1 \leq x < 2^{64}$ 成立。
$f=1$ 这种情况下，有 `x_new=x*M+start`，而现在我们的通式是：`bias + x + q*cmpl_freq`，要选择好 `bias` 的值使它们相等，这样就能使用一个通式了。
```cpp
        //     x*M + start                   (desired result)
        //   = bias + x + q*cmpl_freq        (*)
        //   = bias + x + (x - 1)*(M - 1)    (plug in q=x-1, cmpl_freq)
        //   = bias + 1 + (x - 1)*M
        //   = x*M + (bias + 1 - M)
        // so we have start = bias + 1 - M, or equivalently
        //   bias = start + M - 1.
```

这样就可以了。
[Data Compression With Arithmetic Coding | Mark Nelson](https://marknelson.us/posts/2014/10/19/data-compression-with-arithmetic-coding.html)

记住一点：$V$ 与 $L,H$，满足线性关系，也就是说只要 $L,H$ 进行相同的线性变换，$V$ 保持同步即可。
另外，$V$ 始终是真正的那个 code。

假设某个过程中 
L=0 XXXXX
R=0 XXXXX
实际上可以直接移动，因为我们计算的那个 RANGE=R-L，只要 L, R 同步，就没问题。

比较难理解的是这个：
![[Pasted image 20240414181919.png]]
也就是当：
L=011111 XXX
R=100000 XXX
时的处理

按照算法，变成：
Ln =0 XXX 00000
Rn =1 XXX 11111

这其实也很简单，因为旧的 R-L=
100000 XXX 111111   -
011111   XXX 0000
（后面接的 0，1 表示无穷个）
这其实和 Rn-Ln=
1 xxx 111111
0 xxx 00000
是一样的。

如果 Ln+RANGE * p=
0 XXX
YYYY =
1 ZZZ
也就是 Ln 的第一个 0 进了位，
那么，必然有：
L+RANGE * p =
011111 XXX
      YYYY  =
100000 ZZZ
这是因为 L 的 X 前面也会进位

这就意味着我们的 V 是 1 个 1，接 K 个 0.

同理，如果 Ln+RANGE* $p_1$ =
0 XXX
Y YYY =
0 ZZZ
也就是没有产生进位那么L+RANGE*$p_1$=
011111 XXX
      YYYY =
011111 ZZZ
因为此时那个地方不会有进位，
那么V就是一个0，接K个1。

由于运算得到的后面那些位完全一样，那么L,R完全可以被Ln和Rn替换。

如果前两种都不满足，而是满足第三种情况，
也就说我们得到的：
ZZZZ
依旧无法确定第一个数是啥，继续进入这种pending_bit++的模式，直到某次到达前两种情况之一。
假设是第一种，也就是第一个Z是1，此时就可以不断往前进位了。否则第一个Z是0，就不需要进位。


虽然可能认为L=0000，R=FFFF，并不满足原本区间为10000的假设，但实际上就是按照R后面有无穷的1处理的：
range 加了1
![[Pasted image 20240415122124.png]]
newhigh要减去1，因为后面假设有无穷个1.
![[Pasted image 20240415122153.png]]

另一点需要注意的是，在解码阶段，对value的计算方式：
![[Pasted image 20240415124551.png]]
可能会疑惑，这个value为什么不这么算：offset* total/range
我们是把在range域的offset映射到total域的value，
假设我们已经得到了value，那么从total域的value映射到RANGE域的offset的映射函数是已经固定了的：
(下面的除法都表示机器除)
offset1 = value* range / total
为了保证offset1<=offset，右边最多可以拓展到：
value* range < (offset+1)* total,
也就是
value* range <= (offset+1) * total -1
也就是
value = ((offset+1) * total -1)/range

如果说，我们采用value = offset* total /range的做法，则会得到：
offset1 = value* range /total <= offset
其上限就只有offset，并且大概率是小于的。
而采用第一种计算方式，offset1 < offset+1,offset1有更大概率和offset相等。


以[GitHub - nayuki/Reference-arithmetic-coding: Clear implementation of arithmetic coding for educational purposes in Java, Python, C++.](https://github.com/nayuki/Reference-arithmetic-coding)中对算术编码的实现为例子，系统性的严格来证明该算法的正确性。
在证明之前，先对一些基本问题进行阐述。
首先，需要明确的第一个问题是，尽管在算法中 $L,R$ 的状态依旧是有限的，但 $L$ 的后面有无穷个 $0$, $R$ 后面有无穷个 $1$。
因此，当我们计算 `range` 的时候，是这样计算的：
```cpp
uint64_t range = high - low + 1;
```
并且，当我们计算出 `newLow` , `newHigh` 的时候，后者要自动减 $1$，因为默认后面接了无穷个 $1$。
```cpp
uint64_t newLow = low + symLow * range / total;

uint64_t newHigh = low + symHigh * range / total - 1;
```
其次，在域 `range` 中，有一个数 `offset`，在域 `total` (概率表频次之和)中有一个数 `value`，它们之间有如下近似关系：
$$
\frac{offset}{range}\approx \frac{value}{total}
$$
当然希望这个约等于号取等，但在有限的位内是无法实现的。
那么在得知 `offset` 之后，如何得到 `value`，反过来，在得到 `value` 后，如何得到 `offset`，使得误差尽可能小？(接下来的乘法和除法，都是指计算机的乘除，而不是严格的数学上的乘除)
首先，从 `value` 到 `offset` 的映射已经被固定了：
$$
offset'=value *range /total
$$
那么怎么确定从 `offset` 到 `value` 呢？显然，在解码阶段，这是相当重要的。你可能会说，直接:
$$
value = offset*total /range
$$
不就行了吗。
但此时，我们使用得到的 `value` 去计算回 `offset1`，可以发现新的 `offset1` 几乎不可能和原来的 `offset` 相等，当且仅当 `offset*total` 能够整除 `range` 时，才能取等，其它情况 $offset1<offset$。

我们不妨换个思路：
$$
\begin{align*}
offset1 &< offset+1\\
value* range / total  &< offset+1\\
value* range &< (offset+1)*total\\
value* range &\leq (offset+1)*total - 1\\\\
\Rightarrow value &= ((offset+1)*total - 1)/range
\end{align*}
$$
如果以这样的方式，从 `offset` 映射到 `value`，可以发现重新映射回去得到的 `offset1<offset+1`，`offset1` 依旧有很大概率能够等于 `offset`。比前面那种几乎不可能的方式好得多。


下面，要正式开始证明这个算法了：

```cpp
void ArithmeticCoderBase::update(const FrequencyTable &freqs, uint32_t symbol) {

// State check

if (low >= high || (low & stateMask) != low || (high & stateMask) != high)

throw std::logic_error("Assertion error: Low or high out of range");

uint64_t range = high - low + 1;

if (!(minimumRange <= range && range <= fullRange))

throw std::logic_error("Assertion error: Range out of range");

// Frequency table values check

uint32_t total = freqs.getTotal();

uint32_t symLow = freqs.getLow(symbol);

uint32_t symHigh = freqs.getHigh(symbol);

if (symLow == symHigh)

throw std::invalid_argument("Symbol has zero frequency");

if (total > maximumTotal)

throw std::invalid_argument("Cannot code symbol because total is too large");

// Update range

uint64_t newLow = low + symLow * range / total;

uint64_t newHigh = low + symHigh * range / total - 1;

low = newLow;

high = newHigh;

// While low and high have the same top bit value, shift them out

while (((low ^ high) & halfRange) == 0) {

shift();

low = ((low << 1) & stateMask);

high = ((high << 1) & stateMask) | 1;

}

// Now low's top bit must be 0 and high's top bit must be 1

// While low's top two bits are 01 and high's are 10, delete the second highest bit of both

while ((low & ~high & quarterRange) != 0) {

underflow();

low = (low << 1) ^ halfRange;

high = ((high ^ halfRange) << 1) | halfRange | 1;

}

}
```

我要证明这样的结论：任意时刻进入到该函数时，设真正的区间（无穷的）是 $L，R$，此刻我们算法内部的有限区间 (也就是 `low`, `high`)是 $L'$, $R'$，还有一个 $numUnderflow$ （简写为 $U$）。该函数进行编码（编码新的元素），对 $L'$, $R'$ ，$U$ 进行操作，完成之后，是完全等价于对 $L,R$ 作基本的（无移位）算术编码的操作。

分为两种情况，$U=0$ 和 $U!=0$.
如果是前者，此刻 $L$ 和 $L'$，$R$ 和 $R'$ 那些正在处理的位是相等的，已经处理完毕的那些位 (前面那些)被存储起来，和 $L,R$ 的那些位完全一致。
如果发生第一种情况，$L'$ ，$R'$ 的最左边的位是一致的。就进行左移操作。这种情况的等价性是容易理解的，左移之后，$R'-L'=R-L$。
如果发生 $L'$ 的最高位是 $0$，$R'$ 的最高位是 $1$，就有两种可能：
1. $L'$ 的最高两位是 $01$，$R'$ 是 $10$，则 $U++$，$L',R'$ 保留最高位，并去除第二高的位。
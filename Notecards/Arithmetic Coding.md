[Data Compression With Arithmetic Coding | Mark Nelson](https://marknelson.us/posts/2014/10/19/data-compression-with-arithmetic-coding.html)

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

还有一个需要注意的点，是对于输入流和输出流的一些处理，如
```cpp
if (std::numeric_limits<char>::is_signed)

	currentByte -= (currentByte >> 7) << 8;

output.put(static_cast<char>(currentByte));
```
这是因为如果 char 是带符号的，那么它的范围是 $[-127.128]$，而假设我们需要写入的字节 (在程序中用 int 表示)超过 128，那么把这个 int 形的 `currentByte` 转化为 char 就有可能出现问题，造成转化后的 char 的那 8 个 bit 不完全等于 `currenByte` 的低 8 位。
程序中的这种处理就避免了这种情况，如果 `currentByte` 的第 8 位是 1，`currentByte -= 2^8`，这是一个负数，范围一定在有符号的 char 内，然后负数在 8 位的 char 内部这样表示 (补码)：`2^8-(2^8-currentByte)=currentByte`，这样，在计算机内部它的表示就完全和原本的低 8 位一样了。
打个比方，设 `currentByte=0x000000FF`, 也就是 255，它先减去 256，就是 `-1`，然后把 `-1` 表示成 8 位的带符号数，计算机内部用补码存储，也就是 `256-1=11111111`，完全和低 8 位一样。

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

我要证明这样的结论：任意时刻进入到该函数，进行任何一次操作时（一次这个函数内部可能有很多次操作），设真正的区间（无穷的）是 $L，R$，此刻我们算法内部的有限区间 (也就是 `low`, `high`)是 $L'$, $R'$，还有一个 $numUnderflow$ （简写为 $U$）。任何一次操作（是对对 $L'$, $R'$ ，$U$ 进行的)，是完全等价于对 $L,R$ 作基本的（无移位）算术编码的操作。

(以下证明基于一个基本假设：如果 $U=0$，那么 $L$ 和 $L'$，$R$ 和 $R'$ 那些正在处理的位是相等的，已经处理完毕的那些位 (前面那些)被存储起来，和 $L,R$ 的那些位完全一致。在最开始的时候，算法还未处理任何符号，这显然成立，在第一步基于此假设，又结合下面的证明如果，可以得到任意情况这假设都成立。而对于 $U！=0$ 的情况，在下面的证明中可以看到假设这四个数的形态，这是在 $U=0$ 的情况下加上算法的流程直接得到的。)
分为两种情况，$U=0$ 和 $U!=0$.
如果是前者，此刻 $L$ 和 $L'$，$R$ 和 $R'$ 那些正在处理的位是相等的，已经处理完毕的那些位 (前面那些)被存储起来，和 $L,R$ 的那些位完全一致。
如果发生第一种情况，$L'$ ，$R'$ 的最左边的位是一致的。就进行左移操作。这种情况的等价性是容易理解的：左移之后，$R'-L'=R-L$，说明 $range$ 是相等的，我们又保存了 $L'$ 的高位，$L'$ 和 $L$ 就是完全一样的。我们的计算本来也就基于 $L$ 和 $range$。
如果发生 $L'$ 的最高位是 $0$，$R'$ 的最高位是 $1$，就有两种可能：
1. $L'$ 的最高两位是 $01$，$R'$ 是 $10$，则 $U++$，$L',R'$ 保留最高位，并去除第二高的位。这个情况的证明就回到的 $U！=0$ 的情况，之后说明。
2. 不是前面的情况，那就终止，未曾进行操作，无需证明。
发现，$U=0$ 情况的证明需要到 $U!=0$ 的情况中去。

假设 $U!=0$, 不失去一般性，由算法流程，设此时
$$
\begin{align*}
L&=0~1111~XXX\\
R&=1~0000~X'X'X'\\
L'&=~~~~~~~~0~XXX\\\\
R'&=~~~~~~~~1 X'X'X'
\end{align*}
$$
也就是 $U=4$，请注意 $L,R$ 后面的 $0$ 和 $1$ 被省略了，没有被写出来。
>[!note]
> $L$ 和 $L'$，$R$ 和 $R'$ 后面的那些位是相等的，这是由算法的流程决定的。


依旧有一个很重要的点：$R-L=R'-L'$, 

如果：
$$
\begin{align*}
L'_{new}&=L'+range*P_{low}\\
&=~~~~0~XXX\\
&~~~~+Y~YYY\\
&=~~~~1~ZZZ
\end{align*}
$$
也就是第一位为 1，那么必然有：
$$
\begin{align*}
R'_{new}&=L'+range*P_{high}\\
&=~~~~0~XXX\\
&~~~~+Y'~Y'Y'Y'\\
&=~~~~1~Z'Z'Z'
\end{align*}
$$
这是因为 $Y'Y'Y'Y'>YYYY$

而如果在 $L,R$ 上进行算术编码的计算，有：
$$
\begin{align*}
L_{new}&=L+range*P_{low}\\
&=0~1111~XXX\\
&~~~+~~~~~~Y~YYY\\
&=1~0000~ZZZ
\end{align*}
$$
这是因为那个地方发生了进位，
同样，
$$
\begin{align*}
R_{new}&=L+range*P_{high}\\
&=0~1111~XXX\\
&~~~+~~~~~~Y'~Y'Y'Y'\\
&=1~0000~Z'Z'Z'
\end{align*}
$$
那么前面这些本来未知的数就可以写为一个 $1$，加 $U=4$ 个 0。新得到的 $L',R'$ 又左移一位，此刻：
$$
\begin{align*}
L_{new} &= 1~0000~ZZZ\\
L'_{new}&=~~~~~~~~~~~~ZZZ\\
R_{new}&=1~0000~Z'Z'Z'\\
R'_{new}&=~~~~~~~~~~~Z'Z'Z'
\end{align*}
$$
由于已经存储了前面的高位值，所以操作完全等价。

而如果是：

$$
\begin{align*}
R'_{new}&=L'+range*P_{high}\\
&=~~~~0~XXX\\
&~~~~+Y'~Y'Y'Y'\\
&=~~~~0~Z'Z'Z'
\end{align*}
$$
同理，可得完全等价。

最后，是这种情况：

$$
\begin{align*}
&L'+range*P_{low}\\
&=~~~~0~XXX\\
&~~~~+Y~YYY\\
&=~~~~0~ZZZ
\end{align*}
$$
$$
\begin{align*}
&L'+range*P_{high}\\
&=~~~~0~XXX\\
&~~~~+Y'~Y'Y'Y'\\
&=~~~~1~Z'Z'Z'
\end{align*}
$$
那么我们有：
$$
\begin{align*}
&L+range*P_{low}\\
&=0~1111~XXX\\
&~~~+~~~~~~Y~YYY\\
&=0~1111~ZZZ
\end{align*}
$$
$$
\begin{align*}
&L+range*P_{high}\\
&=0~1111~XXX\\
&~~~+~~~~~~Y'~Y'Y'Y'\\
&=1~0000~Z'Z'Z'
\end{align*}
$$
也就是：
$$
\begin{align*}
L_{new}&=0~1111~ZZZ\\
R_{new}&=1~0000~Z'Z'Z'\\
L'_{new}&=~~~~~~~~~0~ZZZ\\
R'_{new}&=~~~~~~~~~1~Z'Z'Z'
\end{align*}
$$ 
这就又回到了原始的情况。
由于这整个图不是无尽的，它终会在那些被证明的那些节点终止，算法正确性得到证明。

我们在进行一些小的讨论，也就是
$$
\begin{align*}
L_{new}&=01111~ZZZZ=01111~1ZZZ\\
R_{new}&=10000~Z'Z'Z'Z'=10000~0Z'Z'Z'
\end{align*}
$$
此刻，由算法，$U=4+1=5$。
这个过程可以一直进行，直到退出此函数。

最后一个点要说明的事，就是在编码器处理完最后一个符号之后，一些剩余的，还未写入输出流的数该怎么办？它们是未知的。

慢慢来说明，编码一个符号，可以看成这个过程：
初始化 $L,R,U$ -->计算新的 $L,R$ -->进行移位，更新 $L,R,U$。
设为 A-->C-->B
编码第 $i$ 个符号的 B 过程和编码第 $i+1$ 个符号的 A 过程是一致的。
那么，设编码 $n$ 个符号， 总体编码流程可以写为：
$A_1$ -->1--> $A_2$ -->2--> $A_3$ -->3......--> $A_{n-1}$ --> n-1--> $A_n$ -->n--> $A_{n+1}$
解码过程是完全对称的，就是多个一个 V 值：
$B_1$ -->1--> $B_2$ -->2--> $B_3$ -->3......--> $B_{n-1}$ --> n-1--> $B_n$ -->n--> $B_{n+1}$
其中，$B_i$ 和 $B_{i+1}$ 之间的数字 i 表示使用此刻的 V 来解码出符号的过程。

在解码最后一个符号，也就是在完成 $B_n$ 时，此刻 $L,R$ 的状态完全和 $A_n$ 之后一致。假设此刻 $L,R$ 是小数点后第 $k$ 到 $k+31$ 位 (假设状态位是 32)。
在任意时刻，真正的 $L，R$，已经确定的那些位，是必然被写入到了输出流中去的，成为了 $V$ 的对应位上的数。
假设，在 $A_{n+1}$ 结束之后，$U=0$，也就是最后的真正的 $L$ 未确定部分(不是编码状态那个数) ，是 0 开头，真正的 $R$ 是 1 开头，我们的 $V$ 需要取它们中间的数，那就可以把 $V$ 直接补一个 1，加无穷个 0.
假设，在 $A_{n+1}$ 结束之后，$U！=0$，也就是最后的真正的 $L$ 未确定部分，是 0 开头，加 $U$ 个 1，$R$ 是 1 开头，加 $U$ 个 0，我们的 $V$ 需要取它们中间的数，那还是可以把 $V$ 直接补一个 1，加无穷个 0.

加 1 的操作是：
```cpp
void ArithmeticEncoder::finish() {
	output.write(1);
}
```
后面加无穷个 0，可以被简略为 31 个 0. 但还是会占用额外的空间。此代码采用了更巧妙的方法：
```cpp
int ArithmeticDecoder::readCodeBit() {
	int temp = input.read();
	if (temp == -1)
		temp = 0;
return temp;
}
```
它并不显式的把 0 写入文件末尾，而是当读到文件结束符的时候，看成是 0 就行了。

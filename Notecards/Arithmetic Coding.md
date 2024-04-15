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
为了保证offset1=offset，就有：
offset * total<=value* range < (offset+1)* total,
也就是
offset * total <= value* range <= (offset+1) * total -1
也就是
value = ((offset+1) * total -1)/range
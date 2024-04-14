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
000001 XXX 111111   -
000000 XXX 0000
（后面接的 0，1 表示无穷个）
这其实和 Rn-Ln=
1 xxx 111111
0 xxx 00000
是一样的。

如果 Ln+RANGE * p=
0 XXX
YYYY =
1 ZZZ
那么，
          
      

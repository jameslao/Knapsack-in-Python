<h4>为什么要写这个系列</h4>

这些程序大部分都是 2013 年跟着 Tim Roughgarden 看<a href="https://www.coursera.org/course/algo2">算法课</a>的时候写的，还算有些心得，尤其是优化的部分，似乎中文书中讲到的不是很多。而且也很久没有写博客了，回顾一下当初学过的东西也还有些收获。

<h4>背包问题是啥</h4>

不知道你还记不记得我们小时候看过的《太阳山》的故事：

<img src="http://www.jlao.net/wp-content/uploads/2015/08/cover.jpg" alt="cover" width="375" height="273" class="aligncenter size-full wp-image-10219" />

<blockquote>从前，有弟兄俩，老大是个贪心的富人，老二是个穷人。

老二没有地，种老大的地。他一年到头在地里做活；可是老是吃不上，穿不上，租子交不够。

有一天，老二上山打柴。天都快黑了，他还坐在山上；他发愁，加上打柴，租子还是交不够。这时候，忽然刮起一阵黑风，飞来一只大鸟。大鸟落在他跟前，对他说：“你不用发愁。太阳山上有很多金子、银子，还有宝石。我背你上太阳山去吧！你从那儿可以拿些金子回来。”

老二爬到大鸟的背上。大鸟叫他闭上眼睛。他刚闭上眼睛，觉得飕一下子，就听见大鸟说：“到了。你看，有多少金子！去拿吧！你可不要贪心。这是太阳山。如果我们在这儿待得时间太长，赶上太阳回来，就把你烧死了。那时候，我也没办法救你。”

老二一边答应着，一边从大鸟的背上跳下来。哎呀！遍地是金子、银子、宝石，金光闪闪，晃得人睁不开眼睛。老二想了想，就拿一小块金子，装进口袋，要大鸟背他回去。大鸟问他为什么只拿一小块儿。他说够了。

老二又爬到大鸟的背上，闭上眼睛。飕一下子，大鸟落到他家门口了。他谢过大鸟，大鸟就飞走了。

老二有了金子，买了地，盖了房子。他早晨起来去种地，晚上回到新盖的房子里休息，日子过得很好。

老大见老二有金子，很眼红。他问老二的金子是哪儿来的。老二把大鸟背自己到太阳山的事告诉他了。

第二天，老大学着老二到山上去打柴。天快黑了，他故意不回家，坐在山上假装发愁。大鸟飞来了，也答应背他上太阳山去拿些金子。

大鸟背老大到了太阳山，嘱咐他不要贪心，像嘱咐老二一样，可是他连哼也不哼。他看见那么多金子、银子、宝石，忙着张开大布袋子，捡最大块儿的金子往里装。

老大拼命的往大布袋子里装金子，装个没完。大鸟催他回去，他说再让他拿几块。大鸟再催他回去，他还是这么说。最后，大鸟说：“时候到了！太阳马上就回来了！”他这才站起来，朝着大鸟走，摇摇晃晃的，大布袋子压得他走不稳了。没走几步，他又弯下腰来，说：“等一会儿。我再拿几颗宝石吧！”

正在这时候，火红的太阳回来了。它把烈火似的阳光喷到太阳山上。大鸟飞走了。老大被烧死了。</blockquote>

要是换你去拿，你会怎么拿呢？你是只拿一小块，还是拿到自己走不动？有没有什么办法，能够拿到最多的金子呢？
<!--more-->

所谓背包 (knapsack) 问题，就是说一个要去爬山的人（或者大盗之类的也行），他有很多东西可以带，他的包也足够大，但是背得动的总重量有一个上限 $W$ （大家都是人）。假设我们一共有 $ n$ 种物品，每件物品 $ j$ 都有一个非负的重量 $ w_j$（氢气球之类不算）和一个非负的价值 $ v_j$ （我就不举例了）。那么应该怎样挑选，才能让背包中装的物品的总价值最高？

也就是说要让目标函数

$$
\sum_{j=1}^n v_j x_j
$$

最大化。

如果每种物品只有一件，要么带要么不带，那就是最基本的 <strong>0-1 背包问题</strong> 。这时要满足约束条件

$$
\sum_{j=1}^n w_j x_j \le W, \; x_j \in \{0, 1\}.
$$

如果物品 $j$ 有最多 $b_j$ 件可拿，就称为<strong>多重背包问题</strong>或者<strong>有界背包问题</strong>，约束条件是

$$
\sum_{j=1}^n w_j x_j \le W, \; x_j \in \{0, 1, \ldots, b_j\}.
$$

如果可以敞开拿呢，就称为<strong>完全背包问题</strong>或者<strong>无界背包问题</strong> 。

<h4>这问题有啥搞头</h4>

我这么宅的人，一年也不会出去背包几趟，我也没那个机会去太阳山拣金子宝石或者去抢珠宝店，犯得着费这个劲研究这东西吗？

但是——比如说你有一笔钱来投资，有 $n$ 种可能的投资选择，投资 $j$ 需要 $w_j$ 的资金，会创造 $v_j$ 的收益（这儿先不说风险的事儿……）。那解决了这个背包问题，不就得出了一个获得最大收益的最优组合吗？还有比如货轮装货，也是类似的问题。

除了前面提到的这些实际应用，0-1 背包问题的意义还在于它是最简单的<strong>整数规划问题</strong>，并且可以为许多复杂问题提供一个解决的途径。

可是我们有电脑呀，算一下还不快吗？最暴力的方法就是检查所有可能的 $x_j$ 组合，选出来最优的就行了。可是这个组合有 $2^n$ 个，只要 $n$ 稍微大一点点，就已经超出了计算机能够解决的范畴了（你可以算一下比如 $2^{64}$ 会怎么样……）。

多年来，人们对这个看似简单的问题做出了相当多的研究，也得出了若干种能够比较高效解决这个问题的方法，成千上万个物品也不在话下了。我们后面一一加以介绍。

<h4>哪能搞法——动态规划初试</h4>

现在一共有 $n$ 个物品要考虑，总重量不超过 $W$。我们倒着来考虑一下，最优的背包中，包含不包含第 $n$ 个物品呢？

<ul>
    <li>如果<strong>不</strong>包含第 $n$ 个物品，那么这个物品就可以直接去掉了，最优解就是只有前 $n-1$ 个物品放在 $W$ 的背包中的结果；</li>
    <li>如果包含第 $n$ 个物品，那背包就被它占掉了 $w_n$ ，现在的问题就是要把前 $n-1$ 个物品放在 $W - w_n$ 的背包中怎么放法了。</li>
</ul>

看出来了吗？这两种情况中，总价值比较高的就是我们所要的最优解。如果用 $f_n(W)$ 来表示总价值的话，那么

$$
f_n(W) = \max \begin{cases}
f_{n-1} (W) \\
f_{n-1} (W - w_n) + v_n
\end{cases}
$$

这就是我们所要的<strong>状态转移方程</strong>。

空说不太好说，我们来举一个具体的例子吧。为了方便起见，我们规定输入的格式是这样的：第一行的两个数分别是重量上限 $W$ 和物品总数 $n$，接下来的每一行则是物品的价值 $v_j$ 和重量 $w_j$。比方说<a href="http://www.jlao.net/wp-content/uploads/2015/08/knapsack_tiny.txt">输入文件</a>是：

<pre class="nums:false nums-toggle:false lang:default decode:true">16 4
30 4
20 5
40 10
10 3</pre>

意思是我们的背包总重不超过 16，有这样几件物品：

<table border="2" frame="hsides" rules="groups" cellspacing="0" cellpadding="6" align="center"><colgroup> <col /> <col /> <col /> </colgroup>
<thead>
<tr>
<th style="text-align: left;" scope="col">物品</th>
<th scope="col">价值</th>
<th scope="col">重量</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>30</td>
<td>4</td>
</tr>
<tr>
<td>2</td>
<td>20</td>
<td>5</td>
</tr>
<tr>
<td>3</td>
<td>40</td>
<td>10</td>
</tr>
<tr>
<td>4</td>
<td>10</td>
<td>3</td>
</tr>
</tbody>
</table>

我们按照前面的思路，写出来的代码是这样的：

<pre class="lang:python decode:true">#!/usr/bin/env python3

import sys

def knapsack(v, w, limit, n):
    F = [[0] * (limit + 1) for x in range(n + 1)] 
    for i in range(0, n):                # F[-1] is all 0.
        for j in range(limit + 1):
            if j &gt;= w[i]:
                F[i][j] = max(F[i - 1][j], F[i - 1][j - w[i]] + v[i])
            else:
                F[i][j] = F[i - 1][j]
    return F

if __name__ == "__main__":
    with open(sys.argv[1] if len(sys.argv) &gt; 1 else sys.exit(1)) as f:
        limit, n = map(int, f.readline().split())
        v, w = zip(*[map(int, ln.split()) for ln in f.readlines()])

    F = knapsack(v, w, limit, n)
    print("Max value:", F[n - 1][limit])

    """ Display selected items"""
    y = limit
    for i in range(n - 1, -1, -1):
        if F[i][y] &gt; F[i - 1][y]:
            print ("item: ", i, "value:", v[i], "weight:", w[i])
            y -= w[i]</pre>

这里利用了 python 允许数组下标为负，即 <code>F[-1]</code> 实际上就是 <code>F[n]</code>，可以减少一些边界条件的处理。最后显示加入了哪些物品时，只需对所有物品进行回溯，看看在哪个物品上导致 <code>F</code> 增加，即说明这个物品被加入了背包里。

运行结果是

<pre class="nums:false nums-toggle:false lang:default decode:true">Max value: 12248
Max value: 70
item:  2 value: 40 weight: 10
item:  0 value: 30 weight: 4</pre>

我顺便打印了 <code>F</code> 好让大家有一个更直观的感觉：

<pre class="nums:false nums-toggle:false lang:default decode:true">[0, 0, 0, 0, 30, 30, 30, 30, 30, 30, 30, 30, 30, 30, 30, 30, 30]
[0, 0, 0, 0, 30, 30, 30, 30, 30, 50, 50, 50, 50, 50, 50, 50, 50]
[0, 0, 0, 0, 30, 30, 30, 30, 30, 50, 50, 50, 50, 50, 70, 70, 70]
[0, 0, 0, 10, 30, 30, 30, 40, 40, 50, 50, 50, 60, 60, 70, 70, 70]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
</pre>

这里有一个稍微大一点的<a href="http://www.jlao.net/wp-content/uploads/2015/08/knapsack_small.txt">例子</a>，运行之后会输出结果：

<pre class="nums:false nums-toggle:false lang:default decode:true">Max value: 12248
item:  13 value: 3878 weight: 9656
item:  12 value: 1513 weight: 3926
item:  7 value: 2890 weight: 7280
item:  5 value: 1022 weight: 2744
item:  2 value: 2945 weight: 7390</pre>

看到这两个循环，很明显这个算法的时间复杂度是 $O(nW)$，这个数组 <code>F</code> 的空间复杂度也是 $O(nW)$。能不能稍微改得好一点呢？

从上面这个循环中我们发现，最重要的值实际上是 <code>F[n-1]</code>，前面的那些值我们并不太关心。那能不能不要保留这些值呢？如果我们原地刷新 <code>F[i][j] = max(F[i][j], F[i][j - w[i]] + v[i])</code> …… 不行，这样的话，如果左边的 <code>j</code> 先被刷新，右边再碰到同样的 <code> j - w[i] </code> 就已经不是上一轮的值了。那么……要是换从右边开始更新呢？那就没问题啦！咱们的空间复杂度就降到 $O(W)$ 啦！

<pre class="lang:python decode:true ">def knapsack(v, w, limit, n):
    F = [0] * (limit + 1)
    for i in range(n):
        for j in range(limit, w[i], -1):
            F[j] = max(F[j - w[i]] + v[i], F[j])
    return F</pre>

基本上，一般的书讲背包算法的动态规划也就到此为止了。真的就到此为止了吗？$O(nW)$ 就让我们满意了吗？你想想看，这玩意和 $W$ 相关，可 $W$ 是一个<strong>数字</strong>啊！我们哪天要是不开心买了个<a href="http://www.jlao.net/wp-content/uploads/2015/08/knapsack_big.txt">大包包</a>难道就要算好久好久了吗？（有兴趣的可以试试……）

欲知后事如何，且听<a href="http://www.jlao.net/zh/technology/10048">下回分解</a>。
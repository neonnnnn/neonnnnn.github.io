---
title: "被覆数・充填数"
date: 2021-11-07T00:46:02+09:00
tags:
    - 最適化
    - 確率
    - 統計
    - 機械学習
categories:
    - 応用数学
mathjax: true
---

# はじめに
応用数学的な分野でしばしば用いられる道具として、**Covering Number**（**被覆数**）と**Packing Number**（**充填数**）と呼ばれるものがあります。ちょっと嘘があるのですが、一言で表現すると、 距離空間上の集合に対して、被覆数は（ある指定された半径の）球を何個持ってこれば覆うことができるか、充填数は球を何個詰め込むことができるか、を表します。[前回の記事の0階の最適化アルゴリズムの解析](/posts/nesterov-book-lower-bound/#0階の最適化アルゴリズムの場合の下界)のユークリッドノルムへの拡張が、充填数の簡単な応用例になっているなと思い、記憶の呼び起こしも含めて簡単にまとめてみることにしました。思いの外長くなってしまったので（体感）、実際に応用してみるのは次回にして、今回は被覆数・充填数の導入にとどめます（記事数稼ぎ）。

文献[1]が簡潔にまとめてくれているので、表記、ステートメントおよびその番号についてはそちらを参照して書くことにします（ところどころ簡略化しますが）。

# 被覆数と充填数
前述の通り、距離空間上の集合と$\varepsilon>0$に対して、被覆数はその集合を覆うために必要な半径$\varepsilon$の球の個数です。集合を球で覆っているときの、球の中心の集合を$\varepsilon$-netと呼びます。フォーマルに定義すると以下のようになります。

定義 4.2.1（$\varepsilon$-net）
: $(T, d)$を距離空間とする。$T$の部分集合$K \subset T$とある正の実数$\varepsilon > 0$を考える。$K$の部分集合$\mathcal{N} \subset K$は、$K$の各点が$\mathcal{N}$のある点と距離が$\varepsilon$以下であるとき、すなわち、
\begin{align}
    \forall x \in K\ \exists x_0 \in \mathcal{N}: \ d(x, x_0) \le \varepsilon
\end{align}
であるとき、$K$の$\varepsilon$-netであるという。

定義 4.2.2（被覆数）
: $K$の$\varepsilon$-netの最小の濃度を$K$の（$\varepsilon$-）被覆数と呼び、$\mathcal{N}(K, d, \varepsilon)$と書く。

被覆数をもう少しフォーマルに書くと、以下のようになります：
\begin{align}
    \mathcal{N}(K, d, \varepsilon) = \min \\{|X| : X \subseteq K, \ \text{$X$は$K$の$\varepsilon$-net} \\}.
\end{align}

さて、$K$は任意の$\varepsilon$に対してその被覆数が有限であるときかつそのときに限りプレコンパクト（$K$の閉包がコンパクト）です。解析学・集合と位相では、コンパクトであること（とそこから導かれる良い性質）だけを気にして、実際に何個の開集合で覆うことができるかということはあまり考えていなかった気がします。具体的に個数を考えるところがなんとなく応用数学っぽいなと感じます（素人並の感想）。

次に、同様にして充填数を定義します。被覆数は覆うために必要な球の最小の個数でしたが、充填数は詰め込むことのできる半径$\varepsilon/2$の球の最大の個数です（後述しますが、これはちょっと嘘です）。球の中心の集合を$\varepsilon$-separatedと言います。

定義4.2.4（$\varepsilon$-separatedと充填数）
: 距離空間$(T, d)$の部分集合$\mathcal{N}$は、すべての相異なる二点$x, y \in \mathcal{N}$について$d(x, y) > \varepsilon$であるとき、$\varepsilon$-separatedであるという。また、$T$の部分集合$K \subset T$の$\varepsilon$-separatedの最大の濃度を$K$の（$\varepsilon$-）充填数といい、$\mathcal{P}(K, d, \varepsilon)$と書く。

ステートメントを素直に訳すならば、「互いに$\varepsilon$以上離れるように配置した点の集合」が$\varepsilon$-separatedで、充填数はその最大濃度です。

# 被覆数と充填数の関係
被覆数と充填数には面白い関係があります。

補題4.2.6（separatedから作るnet）
: $\mathcal{N}$を最大の$\varepsilon$-separated（$\mathcal{N}$に新たな点を加えると、それがどのような点であっても、$\mathcal{N}$は$\varepsilon$-separatedでなくなる）であるする。このとき、$\mathcal{N}$は$\varepsilon$-netである。

証明
: 任意の$x \in K$について、ある$x_0 \in \mathcal{N}$が存在して、$d(x, x_0) \le \varepsilon$であることを示せば良い。$x \in \mathcal{N}$である場合は自明なので、そうでない場合を示す。$\mathcal{N}$は「最大」の$\varepsilon$-separatedなので、$\mathcal{N} \cup \\{x\\}$は$\varepsilon$-separatedでない。すなわち、ある$x_0 \in \mathcal{N}$が存在して、$d(x, x_0) \le \varepsilon$となり、まさしくこれが示したかった不等式である。

上の補題より、適当に$x_1 \in K$を選ぶ、$x_1$から$d(x_1, x_2) > \varepsilon$なる点を$x_2 \in K$を適当に選ぶ、$d(x_1, x_3)>\varepsilon$かつ$d(x_2, x_3)>\varepsilon$なる点$x_3 \in K$を選ぶ…という手順を、点を選ぶことができなくなるまで繰り返せば、$\varepsilon$-netを構成することができることがわかります（$K$が（プレ）コンパクトであれば、これは有限のステップで止まります）。

被覆数は$\min$を使って定義され、充填数は$\max$を使って定義されています。そのため、被覆数を上から抑えることは簡単ですが、下から抑えるのは難しいです：適当な$\varepsilon$-netを作ればその濃度が$\mathcal{N}(K, d, \varepsilon)$の上界になりますが、$N$で下から抑えるためには$N$個の点をどのように配置しても$\varepsilon$-netにはならないことを示す必要があります。同様に、充填数を下から抑えることは簡単ですが（適当に$\varepsilon$-separatedを持ってこれば良い）、上から抑えることは難しいです。しかし、実は、被覆数と充填数は互いに互いの上下界になっています。

補題4.2.8（被覆数と充填数の等価性）
: 任意の$T \subset K$と$\varepsilon >0$について、以下が成り立つ：
\begin{align}
    \mathcal{P}(K, d, 2\varepsilon) \le \mathcal{N}(K, d, \varepsilon) \le \mathcal{P}(K, d, \varepsilon).
\end{align}

証明
: 右の不等式は補題4.2.6より直ちにわかる。最大の$\varepsilon$-separatedを適当に取ると、補題4.2.6よりそれは$\varepsilon$-netになっている。その濃度を$N$とすると、被覆数と充填数の定義より、$\mathcal{N}(K, d, \varepsilon) \le N \le \mathcal{P}(K, d, \varepsilon)$であり、右の不等式が得られる。

: 左の不等式について考える。$2\varepsilon$-separatedと$\varepsilon$-netを適当に選んで、それぞれ$\mathcal{P}$、$\mathcal{N}$とする。netの定義より、任意の$x \in \mathcal{P}$はある$y \in \mathcal{N}$を中心とする球に属する。さらに、任意の球は$2\varepsilon$より大きく離れた二点を含まない（三角不等式より明らか）。したがって、$\varepsilon$-netの各点$y \in \mathcal{N}$を中心とする球は、$2\varepsilon$-separatedである$\mathcal{P}$の点を最大で一つしか含まない。ゆえに、$|\mathcal{P}| \le |\mathcal{N}|$である（鳩の巣原理）。これは任意の充填$\mathcal{P}$と被覆$\mathcal{N}$について言えるので、$\mathcal{P}(K, d, 2\varepsilon) \le \mathcal{N}(K, d, \varepsilon) $である。

# 被覆数と体積
$T=\mathbb{R}^n$、$d(x, y) = \lVert x - y \rVert_2$とします。すなわち、$n$次元ユークリッド空間について考えます。被覆数は、直感的には$K \subset T$の複雑さ・大きさのようなものを表しているように思えます。そうなると、古典的な体積との関係が気になります。関係を示す前に、2つ記法を導入します。

1. ミンコフスキー和：$A, B \subseteq \mathbb{R}^n$に対して、$A+B$はミンコフスキー和を表す：
\begin{align}  
    A+B := \\{a + b : a \in A, b \in B\\}.
\end{align}
2. 中心が$x_0 \in \mathbb{R}^n$で半径が$r >0 $の球を$B_2^n(x_0, r)$で表す：
\begin{align}  
    B_2^n(x, r) := \\{x_0 + x: \lVert x \rVert_2 \le r\\}.
\end{align}
また、$x_0 = 0$、$r=1$のとき、単に$B_2^n$と書く。

以下の命題が被覆数と体積の関係を示しています。

命題4.2.12（被覆数と体積）
: $K$を$\mathbb{R}^n$の（体積の測れる）部分集合とし、$\varepsilon >0$とする。このとき、
\begin{align}
    \frac{|K|}{|\varepsilon B_2^n|}\le \mathcal{N}(K, \varepsilon) \le \mathcal{P}(K, \varepsilon) \le \frac{|K + (\varepsilon/2)B_2^n|}{|(\varepsilon/2) B_2^n|},
\end{align}
ここで$|\cdot|: \mathbb{R}^n \to \mathbb{R}_{\ge 0}$は$\mathbb{R}^n$での体積を表す。

証明
: 真ん中の不等式は補題4.2.8によるものなので、左（下界）と右（上界）の不等式を示す。

: **（下界）** $N:=\mathcal{N}(K, \varepsilon)$とする。このとき、$K$は半径$\varepsilon$の球$N$個で覆われる。体積を比較すると、明らかに
\begin{align}
    |K| \le N \cdot |\varepsilon B_2^n|
\end{align}
である。両辺を$|\varepsilon B_2^n|$で割ると左の不等式を得る。

: **（上界）**
$N:=\mathcal{P}(K, \varepsilon)$とする。このとき、半径が$\varepsilon/2$で、中心が$K$に属する、互いに素な$N$個の球が存在する。このとき、球は$K$から「はみ出る」可能性があるので、「詰め込んだ球の体積」$\le$「$K$の体積」、すなわち$\sum_{i=1}^N |B_2^n(x_i, \varepsilon/2)|= N \cdot |(\varepsilon/2)B_2^n| \le |K|$とはなりません（$\\{x_i\\}$は詰め込んだ球の中心（$\varepsilon$-separated））。しかし、$K$と球のミンコフスキー和については比較可能で、
\begin{align}
    N \cdot |(\varepsilon/2)B_2^n| \le |K+(\varepsilon/2)B_2^n|
\end{align}
であり、両辺を$ |(\varepsilon/2)B_2^n|$で割ると右の不等式を得る。

# 被覆数・充填数の上界・下界の例
先程の体積との議論をベースにして、いくつかの簡単な集合の被覆数・充填数の上界・下界を導出してみます。被覆数・充填数の上界・下界は、被覆数・充填数の応用において重要な役割を果たします。

## ユークリッド球

系4.2.13（ユークリッド球の被覆数）
: 任意の$\varepsilon >0$について、$B_2^n$の被覆数は以下を満たす：
\begin{align}
    \left(\frac{1}{\varepsilon}\right)^n \le \mathcal{N}(B_2^n, \varepsilon) \le \left(\frac{2}{\varepsilon}+1\right)^n.
\end{align}

証明
: $|K|=|B_2^n|$であり、$|\varepsilon B_2^n|=\varepsilon^n |B_2^n|$である。ゆえに、下界は補題4.2.12よりしたがう。上界も同様に補題4.2.12から導出できる：
\begin{align}
    \mathcal{N}(B_2^n, \varepsilon) \le \frac{|B_2^n + (\varepsilon/2)B_2^n|}{|(\varepsilon/2) B_2^n|} = \frac{|(1+\varepsilon/2)B_2^n|}{|(\varepsilon/2) B_2^n|} = \frac{(1+\varepsilon/2)^n}{(\varepsilon/2)^n} = \left(\frac{2}{\varepsilon}+1\right)^n
\end{align}

系4.2.13より、ユークリッド球の被覆数は$n$に対して指数オーダーであることがわかります。

## ハミングキューブ
次に、バイナリ文字列の空間について考えます。

定義4.2.14（ハミングキューブ）
: 長さ$n$のすべてのバイナリ文字列の集合$\\{0, 1\\}^n$をハミングキューブという。また、2つの異なるバイナリ文字列$x, y \in \\{0, 1\\}^n$について、異なるビットの個数
\begin{align}
    d_H(x, y) :=  \\# \\{i: x(i) \neq y(i)\\}
\end{align}
をハミング距離という。ハミングキューブはハミング距離の元で距離空間となる。$(\\{0, 1\\}^n, d_H)$をハミング空間という。

ハミングキューブの被覆数・充填数の上界・下界については、補題4.2.12と同様の方法で導出することができます。ただし、「体積」は「濃度」に置き換わります。

補題4.2.16（ハミングキューブの被覆数と充填数（[1]では練習問題4.2.16））
: $K=\\{0, 1\\}^n$とし、$\varepsilon >0$とする。このとき、
\begin{align}
    \frac{2^n}{\sum_{k=0}^m \binom{n}{k}} \le \mathcal{N}(K, d_H, \varepsilon) \le \mathcal{P}(K, d_H, \varepsilon) \le \frac{2^n}{\sum_{k=0}^{\lfloor m/2\rfloor} \binom{n}{k}}
\end{align}
が成り立つ。

証明
: 下界を示す。$\mathcal{N} = \\{x_i \\} _ {i=1} ^N$を$K$の（$d_H$の意味での）$m$-netとする。このとき、各$x_i$から定まる、半径$m$のハミング球を$B_H^n(x_i, m)$と書く：
\begin{align}
    B_H^n(x_0, m) := \\{x: x \in K, d_H(x_0, x) \le m\\}.
\end{align}
任意の$x \in K$は、ある$x_i \in \mathcal{N}$が存在して、$x \in B_H^n(x_i, m)$であるから、
\begin{align}
    |K| \le \sum_{i=1}^{N} |B_H^n(x_i, m)|
\end{align}
が成り立つ（ここでは$|\cdot|$は濃度を表す）。任意の$x_i$について、
\begin{align}
    |B_H^n (x_i, m)| = \sum_{k=0}^m | \\{x: x \in K, d_H(x_0, x) = k\\}| \le \sum_{k=0}^m \binom{n}{k}
\end{align}
であるから、
\begin{align}
    |K| =2^n \le N\sum_{k=0}^ m \binom{n}{k}
\end{align}
を得る。両辺を$\sum_{k=0}^m \binom{n}{k}$で割ると$2^n /\sum_{k=0}^m \binom{n}{k} \le N$であり、これは任意の$m$-netについて成立するので、下界を得る。

: 上界も同様にして導出ができる（ミンコフスキー和は考える必要がないことに注意）。

# 補足：充填数の説明に関する嘘
最後に補足的な事項を述べておきます。充填数に関する直感的な説明として、「詰め込むことのできる半径$\varepsilon/2$の球の最大の個数」という表現を用いました。$\varepsilon$-separatedの定義が「$\varepsilon$より大きく離れた点の集合」であり、充填数の定義が「$\varepsilon$-separatedの最大の濃度」であることから、直感的には正しそうに思えますが、実は正しくありません。例えば、離散距離
\begin{align}
    d(x, y) = \begin{cases}
        1 & \text{if} \ x \neq y, \\\\\\
        0 & \text{if} \ x = y
    \end{cases}
\end{align}
を考えると、$1<\varepsilon<2$のもとで反例となることがわかります。$1 < \varepsilon < 2$のとき、半径$1/2<\varepsilon/2<1$の球は1点集合なので、互いに素な半径$\varepsilon/2$の球の中心の集合で、濃度が最大のものは、$K$自身になります。しかし、任意の異なる2点の距離は$1$なので、$K$は$\varepsilon$-separatedではありません。

しかし、幸い、ノルム空間においてはこの表現は正しくなります（距離は当然ノルムから誘導された距離とする）。
1. $\mathcal{P}$が$\varepsilon$-separatedならば、$\mathcal{P}$の各点を中心とした$\varepsilon/2$-ballは互いに素です。もし互いに素でないならば、$\mathcal{P}$のある2点を中心とする2の球に共通する点が存在し、そのような点を用いて三角不等式を適用すると2つの中心の距離が$\varepsilon$以下になり矛盾します（これは距離空間でも成立する）。

2. $\mathcal{P}$の各点を中心とした$\varepsilon/2$-ballが互いに素であるならば、$\mathcal{P}$は$\varepsilon$-separatedです。ノルム空間である->線形空間であることから、任意の二点$x, y \in \mathcal{P}$の中点$z=(x+y)/2 \in T$であり、$\lVert x - z \rVert = \lVert y - z \rVert = \lVert x - y \rVert/2$です。2つの球が互いに素であることから、$\lVert x - y \rVert/2 > \varepsilon/2$、したがって$\mathcal{P}$は$\varepsilon$-separatedです。

# おわりに
本記事では、被覆数と充填数の基本的な内容についてまとめました。被覆数と充填数は、距離空間上の集合に対して定義される値であり、被覆数は$\varepsilon$-netの最小の濃度、充填数は$\varepsilon$-separatedの最大の濃度です。直感的には、半径$\varepsilon$の球を何個持ってこれば集合を覆うことができるのか、半径$\varepsilon/2$の球を何個詰め込むことができるのか、を表します。また、被覆数と充填数は互いに上界下界になっており、等価です。ユークリッド球とハミングキューブに関して、被覆数の上界と下界を導出しました。上界・下界は体積・濃度によって表現されており、上界・下界からこれらの被覆数は次元$n$に対して指数的に増大する事がわかりました。

次回の記事では、簡単な応用先として以下の3つ紹介する予定です：
1. 誤り訂正符号（[1]の4.3より）
2. 0階の最適化アルゴリズム
3. ランダムフーリエ特徴[2]

いずれも、誤差の評価っぽいところで被覆数・充填数を用います。

netとseparatedはそれぞれどう日本語訳をつけるのが適切なのかがちょっと気になります。後者は分離集合とか？今度もうちょっとちゃんと調べたい。

# 参考文献
[1] R. Vershynin. High dimensional probability. An introduction with applications in Data Science. Cambridge University Press, 2018.

[2] A. Rahimi and B. Recht. Random Features for Large-Scale Kernel Machines. In NeurIPS, pp. 1177--1184, 2007.

---
title: "0階/1階最適化アルゴリズムの反復数の下界"
date: 2021-10-25T01:56:30+09:00
tags:
    - 最適化
categories:
    - 最適化
mathjax: true
---

# はじめに
様々な最適化問題に対して、それを解くための様々な最適化アルゴリズムが提案されています。
最適化問題の解きやすさ・最適化アルゴリズムの良し悪しの議論の際に、**収束レート**、すなわち、$\varepsilon$-近似解を得るために必要な反復数のオーダーというものを考えることがあります（あるいは、結局は同じことですが、反復数に対する精度$\varepsilon$のオーダー）。
Nesterov先生の本、[Lectures on Convex Optimization](https://www.springer.com/jp/book/9783319915777)に、**0階/1階の最適化アルゴリズム**という比較的広いクラスの最適化アルゴリズムの反復数の下界の証明があり、そういえばステートメントは見たことがあったが証明までは見たことがなかったと思い、自分の言葉でややカジュアルに整理してみます（外せる・緩めることのできる仮定などもありますが、とりあえずはそのままで）。

# 問題設定

## 問題設定
**何らかの連続性**を持つ関数$f:\mathbb{R}^n \to \mathbb{R}$に対して、以下の最適化問題を考えます：
\begin{align}
    \min_{x \in C \subseteq \mathbb{R}^n } f(x).
\end{align}
また、「連続最適化問題を解く」は「$\varepsilon$-近似解（$\varepsilon>0$）を得る」こと、すなわち、
$$f(\bar{x}) - f(x^*) < \varepsilon, \ \text{where} \ x^* = \underset{x\in C} {\mathrm{argmin}}f(x)$$
を満たす$\bar{x} \in C$を得ること、と定義します。
この$\bar{x}$を得るための反復数の下界、すなわち、最悪ケースでどの程度の反復数が必要になるのか、ということについて考えます。

## 考える最適化アルゴリズム
様々な最適化アルゴリズムが存在しますが、その多くは反復法と呼ばれる枠組みに入ります。
反復法は、ひどく一般化して書くと、以下のような手順で最適化を行います：
1. 初期解$x_0$と精度$\varepsilon$が与えられる。$k=0$とする。
2. 現在の解$x_k$で**オラクル**を呼ぶ。オラクルは最適化問題に関する何らかの情報を返す。情報の集合にオラクルから得られた情報を追加する。
3. 情報の集合と、アルゴリズムが定める規則に基づいて、次の解$x_{k+1}$を生成する。
4. 収束条件のチェックをする。$f(x_{k+1}) - f(x^*) < \varepsilon$ならば$\bar{x}:=x_{k+1}$として$\bar{x}$を返す。さもなければ、$k \leftarrow k+1$として、2に戻る。

「オラクルからどのような情報を得るか」「どのように更新するか」が、アルゴリズムの設計において重要になってきます（前者は、設計というよりも問題設定・前提、と言ったほうが正確な気もしますが）。
ここでは、以下の二種類のオラクルについて考えます。
1. 0階オラクル：関数の値$f(x_k)$を返す。
2. 1階オラクル：関数の値$f(x_k)$とその勾配$\nabla f(x_k)$を返す。

0階オラクルを用いる場合（しか用いることができない場合）は、いわゆるブラックボックス関数の最適化問題になるかと思います。
また、1階オラクルを用いる手法はいわゆる勾配ベースの方法になります。
0階オラクルを用いる最適化アルゴリズムを0階の最適化アルゴリズム、1階のオラクルを用いる最適化アルゴリズムを1階の最適化アルゴリズムと呼ぶことにします。


# 0階の最適化アルゴリズムの場合の下界
## 最適化問題に対する仮定
実行可能領域として、以下を考えます：
\begin{align}
    B_n = \\{x \in \mathbb{R}^n \mid 0 \le x^{(i)} \le 1, i=1,\ldots, n\\}.
\end{align}

また、関数$f$は$\ell_\infty$ノルム
\begin{align}
    \lVert x \rVert_{(\infty)} = \max_{1 \le i \le n} |x^{(i)}|
\end{align}
の元で$L$-リプシッツ連続であるとします：
\begin{align}
    |f(x)-f(y)| \le L \lVert x - y \rVert_{(\infty)}, \forall x, y \in B_n, \exists L > 0.
\end{align}

## 主結果
定理1.1.2
: $\varepsilon < L/2$とする。このとき、各0階の最適化アルゴリズムに対して、精度$\varepsilon$の解を得るために、少なくとも$\lfloor L/2\varepsilon \rfloor^n$回のオラクル呼び出しを必要とする関数$f$が存在する。

証明
: $p=\lfloor L/2\varepsilon \rfloor$とします。
最適化アルゴリズムを$N < p^n$回シミュレートして、その結果を元に、上で示された回数の反復が必要な意地の悪い関数を作ることで示します。
具体的には、最小値が$-\varepsilon$であるが、$N$回のオラクル呼び出しの点での最小値は$0$であるような関数を作ります。
この場合、明らかに、$\varepsilon$の精度の解を求められていないので、少なくとも$p^n$回のオラクル呼び出しが必要となる、という主張が導かれます。
: すべてのオラクル呼び出しの点$x_k$、$0 \le k < N$に対して、$f(x_k)=0$を返すとします。
さらに、$B_n$の各次元を$p$分割して、$p^n$個のグリッドを作ります。
このとき、$N < p^n$であるので、少なくとも１つのグリッドは、呼び出された点を$x_k$を一つも含みません。
そのようなグリッドの中心を$x^*$とします（$x_k$を一つも含まないグリッドは一つとは限りませんが、どれでも良いです）。
今、距離は$\ell_{\infty}$ノルムで測っているので、各グリッドの中心と端点の距離は$\varepsilon/L$となります。
そして、以下の関数を考えます：
\begin{align}
    f(x) = \min \\{0, L \lVert x - x^* \rVert_{(\infty)} - \varepsilon\\}.
\end{align}
この関数は、$L$-リプシッツ連続であり、最小値が$-\varepsilon$であり、さらに与えられた0階の最適化アルゴリズムの$N$回のオラクル呼び出しでは$0$を返します。

# 1階の最適化アルゴリズムの場合の下界
## 最適化問題に対する仮定
実行可能領域は$\mathbb{R}^n$全体とします。
また、関数$f$は凸で、無限回微分が可能であり、さらに、勾配が$\ell_2$ノルムの意味で$L$-リプシッツ連続
\begin{align}
    \lVert\nabla f(x) - \nabla f(y) \rVert_2 \le L\lVert x-y\rVert_2
\end{align}
であるとします。
別の言い方をすると、$f$は$L$-平滑であるとします。

## 最適化アルゴリズムに対する仮定
オラクルから得られた情報をどのように使うかについて、やや制限をします。
生成される解は以下を満たすものとします：
\begin{align}
    x_{k+1} \in x_0 + \mathrm{Span}(\nabla f(x_0), \nabla f(x_1), \ldots, \nabla f(x_k)).
\end{align}
すなわち、アルゴリズムは、ある$a_0, \ldots, a_k \in \mathbb{R}$が存在して、以下のように書ける$x_{k+1}$を生成します：
\begin{align}
    x_{k+1} = x_0 + \sum_{i=0}^k a_i \nabla f(x_i).
\end{align}

## 主結果
0階の最適化アルゴリズムの際は、アルゴリズムごとに意地の悪い関数を設計しました・アルゴリズムごとに存在することを示しました。
一方で今回は、「任意の1階の最適化アルゴリズム」に対して意地の悪い挙動をする関数が存在する、という主張になります（ただし、アルゴリズムの生成する解に前述した制限があり、さらに$k$が小さいときの主張になります）。

定理2.1.7
: 任意の$1 \le k \le (n-1)/2$と任意の$x_0 \in \mathbb{R}^n$に対して、前述の仮定を満たす$f$で、前述の仮定を満たす任意の1階の最適化アルゴリズムが生成する解$x_k$について
\begin{align}
    f(x_k) - f(x^* ) &\ge \frac{3L\lVert x_0 - x^* \rVert^2_2}{32(k+1)^2}
\end{align}
となるものが存在する。

## 証明の方針
適当に並行移動をさせれば良いので、一般性を損なうことなく、$x_0=0$とすることができます。
$f(x_k)$と$f^*$、$x_0$と$x_k$について、もし以下が成り立っていれば、主張を導くことができます：

\begin{align}
    f(x_k) - f(x^* ) &\ge \frac{L}{16(k+1)}, \\\\\\
    \lVert x_0 - x^* \rVert^2_2 = \lVert x^* \rVert^2_2 &\le \frac{2}{3}(k+1).
\end{align}

具体的には、以下のような二次関数を考えます：
\begin{align}
    f_{k}(x) := \frac{L}{4} \left \\{ \frac{1}{2} \left[ \left(x^{(1)}\right)^2 + \sum_{i=1}^{k-1} \left(x^{(i)} - x^{(i+1)}\right)^2 + \left(x^{(k)}\right)^2 \right] - x^{(1)}\right\\}.
\end{align}
行列形式で、$f_k$は以下のように書けます：
\begin{align}
    f_k(x) = \frac{L}{4} \left(\frac{1}{2}x_k^\top A_k x_k - x_k^\top e_1\right),
\end{align}
ここで、
\begin{align}
    A_k = \begin{pmatrix}
        B_k & 0_{k, n-k} \\\\\\
        0_{n-k, k} & 0_{n-k, n-k}
    \end{pmatrix}
    \in \mathbb{R}^{n\times n}, \ 
    B_k = \begin{pmatrix}
        2 & -1 & 0 & & &\\\\\\
        -1 & 2 & -1 & 0 & \\\\\\
        0 & -1 & 2 & -1 & 0 \\\\\\
        \\\\\\
        & \cdots & & \cdots & & \\\\\\
        \\\\\\
        & & 0 & -1 & 2 & -1 \\\\\\
        & &  & 0 & -1 & 2
    \end{pmatrix} \in \mathbb{R}^{k \times k},
\end{align}
$0_{k, p}$は$(k \times p)$の零行列です。

この関数が、先程の性質を満たすことを示していきます。


## 補題

$f_k$の性質について調べます。
この問題設定では凸で平滑な関数について考えていましたが、$f_k$はその条件を満たします。

補題：$f_k$の凸性・平滑性
: 任意の$k>0$について、$f_k$は凸であり$L$-平滑である。

証明
: ヘッセ行列に関する二次形式が上下から抑えられることを示せば良い。
\begin{align}
    \langle \nabla^2f_k(x)h, h \rangle = \frac{L}{4} \left \\{ \frac{1}{2} \left[ \left(h^{(1)}\right)^2 + \sum_{i=1}^{k-1} \left(h^{(i)} - h^{(i+1)}\right)^2 + \left(h^{(k)}\right)^2 \right]\right\\} \ge  0
\end{align}
より、ヘッセ行列は半正定値、故に$f_k$は凸。
\begin{align}
    \langle \nabla^2f_k(x)h, h \rangle = \frac{L}{4} \left \\{ \frac{1}{2} \left[ \left(h^{(1)}\right)^2 + 2\sum_{i=1}^{k-1} \left(h^{(i)} + h^{(i+1)}\right)^2 + \left(h^{(k)}\right)^2 \right]\right\\} \le L \langle h, h\rangle
\end{align}
より、$f_k$は$L$-平滑である。

次に、最適解の性質について整理します。
最適解$f^* _k$および$\lVert \bar{x} _k\rVert^2_2$、$\bar{x} _k=\mathrm{argmin}_{x \in \mathbb{R}^n } f_k (x)$は、$1/k$のオーダーで減衰します/上から抑えられます。

補題：$f_k$の最適解の性質
: $\bar{x} _ k$と$f^* _k=f_k(\bar{x} _ k)$について、
\begin{align}
    f^* _k &= \frac{L}{8}\left(-1+\frac{1}{k+1}\right), \\\\\\
    \lVert \bar{x}_{k} \rVert^2_2 &\le \frac{1}{3}(k+1)
\end{align}
が成り立つ。

証明
: \begin{align}
    \nabla f_k(x) = A_k x - e_1
\end{align}
より、最適解は$\bar{x} _k$は
\begin{align}
    \bar{x}^{(i)}_k = \begin{cases}
    1 - \frac{i}{k+1} & i=1, \ldots, k, \\\\\\
    \ \ \ 0 & \text{otherwise.}
    \end{cases}
\end{align}
であり、最適解$f^* _k$は
\begin{align}
    f^*_k = \frac{L}{4} \left\[\frac{1}{2}\langle A_k \bar{x}_k, \bar{x}_k \rangle - \langle e_1, \bar{x}_k \rangle \right] = -\frac{L}{8}\langle e_1, \bar{x}_k \rangle  = \frac{L}{8}\left(-1 + \frac{1}{k+1}\right).
\end{align}
$\bar{x}_k$のノルムについては、
\begin{align}
    \lVert \bar{x}_k \rVert_2^2 &= \sum_{i=1}^k\left(1-\frac{i}{k+1}\right)^2 \\\\\\
    &= k - \frac{2}{k+1}\sum_{i=1}^k i + \frac{1}{(k+1)^2} \sum_{i=1}^k i^2 \\\\\\
    &\le k - \frac{2}{k+1}\cdot \frac{k(k+1)}{2} + \frac{1}{(k+1)^2}\cdot \frac{(k+1)^3}{3} = \frac{1}{3}(k+1).
\end{align}
ただし、最後の不等式では、$\sum_{i=1}^k i^2 = k(k+1)(2k+1)/6 \le (k+1)^3 / 3$を用いた。

次に、生成される解の性質について述べます。
$\mathbb{R}^{k, n}$を、$n$次元のうち後ろの$n-k$個が$0$であるようなベクトルの集合、すなわち、
\begin{align}
    \mathbb{R}^{k, n} := \\{ x \in \mathbb{R}^n \mid x^{(i)}=0, k+1 \le i \le n \\}
\end{align}
とします。
このとき、前述の仮定を満たす任意の1階の最適化アルゴリズムで$k$回目に生成される解は$\mathbb{R}^{k, n}$に属します。

補題2.1.5（生成される解の性質）
: $x_0=0$とする。任意の列$\\{x_k\\}^p_{k=0}$が
\begin{align}
    x_k  \in \mathcal{L}_k := \mathrm{Span}(\nabla f_p(x_0), \ldots, \nabla f_p(x _{k-1}))
\end{align}
を満たすとき、
\begin{align}
    \mathcal{L_k} \subseteq \mathbb{R}^{k, n}
\end{align}
が成り立つ。

証明
: 帰納法で示す。
    1. $k=1$のとき：$x_0=0$、$\nabla f_p(x_0) = -L e_1 / 4$より、$\mathcal{L}_1 = \mathbb{R}^{1, n}$。
    2. $k>0$のとき、$A_k$は三重対角なので、任意の$x \in \mathbb{R}^{k, n}$について、$\nabla f_p (x) \in \mathbb{R}^{k+1, n}$。故に$\mathcal{L}_{k+1} \subseteq \mathbb{R}^{k+1, n}$。

系2.1.1
: 任意の列$\\{x_k\\}^p_{k=0}$、$x_0=0$、$x_k \in \mathcal{L}_k$について、
\begin{align}
    f_p(x_k) \ge f_k^*
\end{align}

証明
: $x_k \in \mathcal{L}_k$より、$x_k$の後ろの$n-k$個の値は$0$である。
したがって、$f_p(x_k) = f_k(x_k)$。また、$f^* _k$は$f_k$の最適解であるから、$f_p(x_k) = f_k(x_k) \ge f^*_k$。


## 主結果の証明
ここまでの補題で、以下がわかりました:
1. $f^* _k$の値（$O(1/k)$）
2. $\lVert \bar{x}_k \rVert_2^2$の上限（$O(1/k)$）
3. （1階の最適化アルゴリズムが生成する$x_k$について）$f_p(x_k) \ge f^* _k$

また、我々が導出したいのは、以下でした：
1. $f(x_k) - f^*$の下限（$O(1/k)$）
2. $\lVert x_k \rVert_2^2$の上限（$O(1/k)$）
わかったことの1つめと3つめより、適当な$p$を選んで$f=f_p$とすれば、導出したいことの１つ目である$f_p(x_k) - f^*(x_k)$の下限を導くことができそうです
また、わかったことの2つめより、導出したいことの2つ目は直ちに導かれます。


（再掲）定理2.1.7
: 任意の$1 \le k \le (n-1)/2$と任意の$x_0 \in \mathbb{R}^n$に対して、前述の仮定を満たす$f$で、前述の仮定を満たす任意の1階の最適化アルゴリズムが生成する解$x_k$について
\begin{align}
    f(x_k) - f(x^* ) &\ge \frac{3L\lVert x_0 - x^* \rVert^2_2}{32(k+1)^2}
\end{align}
となるものが存在する。

証明
: $f=f_{2k+1}$を最適化する関数として考える。このとき、
\begin{align}
    f^* &= \frac{L}{8} \left(-1 + \frac{1}{2k+2}\right)
\end{align}
が成り立つ（最適解の性質の補題より）。
また、系2.1.1より、任意の$x_k \in \mathcal{L}_k$について、
\begin{align}
    f(x_k) = f_{2k+1}(x_k) = f_k(x_k) \ge f^*_k
\end{align}
が成り立つ。
したがって、
\begin{align}
    f(x_k) - f^* \ge f^* _k - f^* = \frac{L}{8}\left(-1+\frac{1}{k+1}+1-\frac{1}{2k+2}\right) = \frac{L}{16(k+1)}.
\end{align}
また、同様に、最適解の性質より、
\begin{align}
    \lVert x^* \rVert^2_2 &\le \frac{2}{3}(k+1)
\end{align}
であるから、これらを合わせて、
\begin{align}
    \frac{f(x_k)-f^*}{\lVert x_0 - x^* \rVert^2_2} \ge \frac{3L}{32(k+1)^2}
\end{align}
と、定理の主張を得る。


# 余談
Nesterov先生の本では、同時に$\lVert x_k - x^* \rVert_2^2$、すなわち、minimizerの精度についても導出しています。

---
title: "被覆数と充填数の簡単な応用"
date: 2022-02-05T4:40:50+09:00
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
[前回の記事]({{< ref "/posts/covering-packing.md" >}})から悠久の時が経ってしまった……（一週間以内に更新する予定だったのですが、うっかりダンガンロンパに手を出してグダグダになってしまった）。前回の記事ではConvering Number（被覆数）とPacking Number（充填数）を紹介しました。今回の記事では被覆数と充填数の簡単な応用として、
1. 誤り訂正符号（[1]の4.3より）
2. 0階の最適化アルゴリズム
3. ランダムフーリエ特徴[2]

の誤差の評価方法を紹介します。

# 誤り訂正符号の誤差の評価
## 問題設定・問題意識
舞園さんは長さ$k$の文字列を苗木くんに送りたいです。このとき、攻撃者モノクマによって、送信したメッセージのうち最大で$r$個の文字が書き換えられてしまい、苗木くんは書き換えられたメッセージを受け取ります。例えば、舞園さんは「**エスパーですから**」という文字列を送るも（$k=8$）、モノクマの悪質なイタズラによって、苗木くんが受け取るメッセージは「**アイドルですから**」になってしまう、といったものです（$k=4$）。

舞園さんはどのようにすれば正しいメッセージを苗木くんに送ることができるでしょうか？一般的な方法は**冗長性**をもたせることです。例えば、**Majority (logic) Decoding**という方法では、舞園さんは元のメッセージを$2r+1$回繰り返したメッセージを送り、苗木くんは繰り返されたメッセージに対して多数決を取ることによって正しい文字列を得ます。上の例にMajority Decodingを適用してみましょう。舞園さんは「エスパーですから」を$2r+1=9$回繰り返した「**エスパーですからエスパーですからエスパーですからエスパーですからエスパーですからエスパーですからエスパーですからエスパーですからエスパーですから**」という文字列を送ります。そうすると、どのように$r=4$個の文字を変更しようとも、$9$個の（長さ$k=8$の）文字列の中で最も出現頻度が多いのは「エスパーですから」になり、苗木くんは正しいメッセージを受け取ることができます。

Majority Decodingはシンプルでわかりやすいですが、直感的にはなんとも効率が悪そうに思えます。
もっと良い方法はないのでしょうか？

## 誤り訂正符号と充填数の関係
上の例では日本語の文字列を考えましたが、ここでは0/1の二値の文字列を考えます。まず、**誤り訂正符号**の定義を以下に示します。

定義4.3.3（誤り訂正符号）
: ある（固定された）正の整数$k, n, r$が与えられる。２つの写像
\begin{align}
  E: \\{0, 1\\}^k  \to \\{0, 1\\}^n \quad \text{and} \quad E: \\{0, 1\\}^n \to \\{0, 1\\}^k
\end{align}
は、任意の$x \in \\{0, 1\\}^k$と、$E(x)$と最大で$r$個の文字が異なる任意の文字列$y$について、$D(y)=x$が成り立つとき、($r$個の誤りを訂正できる)符号写像、復号写像という。$E$を誤り訂正符号、$E$の像$E(\\{0, 1\\}^k)$を符号表（codebook）といい、符号表の各要素（すなわち、$E(x)$）を符号語という。

$k, n, r$はそれぞれ「送りたい文字列の長さ」「誤りを考慮して送る文字の長さ」「発生する最大の誤りの数」に対応します。$n$は小さければ小さいほど、効率の良い方法ということになります。前述のMajority decodingでは$n=k(2r+1)$でしたが、これよりも効率の良い方法が存在するのかが気になります。充填数を使うことで、より効率の良い方法が存在することを主張できます。

補題4.3.4
: 正の数$k, r$に対して、$n$が
\begin{align}
    \log \mathcal{P}(\\{0, 1\\}^n, d_H, 2r) \ge k
\end{align}
を満たすとする。このとき、$k$ビットの文字列を$n$ビットの文字列に符号化し、$r$個の誤りを訂正することのできる、誤り訂正符号が存在する。

証明
: $\mathcal{N} \subseteq \\{0, 1\\}^n$を、$n$次元ハミングキューブ$(\\{0, 1\\}^n, d_H)$のある$2r$-separatedとする。
定義より、$\mathcal{N}$の各要素$x, y \in \mathcal{N}$はハミング距離が$2r$より大きい、すなわち、互いに$2r+1$個以上の文字が異なっている。したがって、$x \in \mathcal{N}$は、最大で$r$個の文字が変わり$x' \in \\{0, 1\\}^n$になったとしても、$\mathcal{N}$の中で最もハミング距離の近い要素は$x$のままである：
\begin{align}
    x  = \underset{y \in \mathcal{N}}{\mathrm{argmin}} \ d_{H}(y, x'), \  \forall x'\in \\{0,1\\}^n \ \text{s.t.} \ d_{H}(x, x') \le r.
\end{align}
ゆえに、$k \le \log |\mathcal{N}|$であるならば、長さ$k$の各文字列を、$\mathcal{N}$の要素を互いに異なるように対応付けることができ（その対応を$E$とする）、$D(y) := E^{-1}\left(\mathrm{argmin}\_ {y' \in \mathcal{N}} d_{H}(y, y')\right)$とすることで、$k$ビットの文字列を$n$ビットの文字列に符号化し、$r$個の誤りを訂正することのできる、誤り訂正符号を構成できる。仮定より、$|\mathcal{N}|=2^{k}$であるような$\mathcal{N} \subseteq \\{0, 1\\}^n$が存在するので、主張を得る。

補題4.3.4より、充填数の対数$\log \mathcal{P}(\\{0, 1\\}^n, d_H, 2r)$（metric entropyと呼ばれます）が$k$以上になるように$n$を設定すれば、誤り訂正符号を構成できることがわかりました。しかし、この充填数を使った不等式だけでは、$n, r, k$の関係がほとんどわからず、実質的にはほとんど何も言えていません。ここで、前回の記事で導出した充填数の下界を利用します。

補題4.2.16（ハミングキューブの被覆数と充填数（[1]では練習問題4.2.16））
: $K=\\{0, 1\\}^n$とし、$\varepsilon >0$とする。このとき、
\begin{align}
    \frac{2^n}{\sum\_{k=0}^m \binom{n}{k}} \le \mathcal{N}(K, d_H, \varepsilon) \le \mathcal{P}(K, d_H, \varepsilon) \le \frac{2^n}{\sum\_{k=0}^{\lfloor m/2\rfloor} \binom{n}{k}}
\end{align}
が成り立つ。

これを利用すると以下の定理を得ます。

定理4.3.5（誤り訂正符号の保証）
: 正の数$k, r$に対して、$n$が
\begin{align}
    n \ge k + 2r \log_2\left(\frac{en}{2r}\right)
\end{align}
を満たすとする。このとき、$k$ビットの文字列を$n$ビットの文字列に符号化し、$r$個の誤りを訂正することのできる、誤り訂正符号が存在する。

証明
: 補題4.2.16より、補題4.3.4の条件は次のように書き換えることができる：
\begin{align}
    n \ge k + \log_2 \left(\sum\_{m=0}^{2r}\binom{n}{m}\right).
\end{align}
また、二項係数の和について、以下の不等式が成立する（$n > m$）：
\begin{align}
    & \sum\_{k=0}^{m}\binom{n}{k} \times \left(\frac{m}{n}\right)^m \le \sum\_{k=0}^{m}\left[\binom{n}{k} \times \left(\frac{m}{n}\right)^k \right] \\\\\\
    & \le \sum\_{k=0}^{n}\left[\binom{n}{k} \times \left(\frac{m}{n}\right)^k \right] = \left(1 + \frac{m}{n}\right)^n \le e^m \\\\\\
    & \rightarrow \sum\_{k=0}^{m}\binom{n}{k} \le \left(\frac{en}{m}\right)^m. \label{eq:binom_upper_bound}
\end{align}
この不等式\eqref{eq:binom_upper_bound}を$m=2r$として用いることで主張を得る：
\begin{align}
    n \ge k + \log_2 \left(\frac{en}{2r}\right)^{2r} \rightarrow  n \ge k + \log_2 \left(\sum\_{m=0}^{2r}\binom{n}{m}\right).
\end{align}

# 0階の最適化アルゴリズムの誤差の評価
[前々回の記事]({{< ref "/posts/nesterov-book-lower-bound.md" >}})では、0階の最適化アルゴリズムの反復数の下界の導出を行いました。その際に、$\ell_{\infty}$ノルム球を実行可能領域とし、また、目的関数は$\ell_{\infty}$ノルムの元でリプシッツ連続としました。では、別のノルムについて、例えば最もよく知られた$\ell_2$ノルムではどうなるのでしょうか。

## 問題設定
[前々回の記事]({{< ref "/posts/nesterov-book-lower-bound.md" >}})から考えるノルムを変えるだけですが、再掲します。関数$f:\mathbb{R}^n \to \mathbb{R}$について、以下の最適化問題を考えます：
\begin{align}
    \min_{x \in B\_n} f(x),
\end{align}
ここで、$B\_n$は（半径1の）$\ell_{2}$ノルム球
\begin{align}
    B\_n = \\{x \in \mathbb{R}^n : \lVert x \rVert\_2 \le 1\\}
\end{align}
であり、また、$f$は$\ell_{2}$ノルムの意味で$L$-リプシッツ連続
\begin{align}
    |f(x)-f(y)| \le L \lVert x - y \rVert\_{2}, \forall x, y \in B\_n, \exists L > 0
\end{align}
であるとします。また、「連続最適化問題を解く」は「$\varepsilon$-近似解（$\varepsilon>0$）を得る」こと、すなわち、
\begin{align}
    f(\bar{x}) - f(x^\* ) < \varepsilon, \ \text{where} \ x^\* = \underset{x\in B\_n} {\mathrm{argmin}}f(x)
\end{align}
を満たす$\bar{x} \in B\_n$を得ること、と定義します。この$\bar{x}$を得るための反復数の下界、すなわち、最悪ケースでどの程度の反復数が必要になるのか、ということについて考えます。特に、0階の最適化アルゴリズム、すなわち、関数の評価値だけを使うアルゴリズムについて考えます。

## 反復数の下界の評価
$\ell\_{\infty}$ノルムの場合の証明方法を復習します。実行可能領域を$\lfloor L/2\varepsilon \rfloor^n$個のグリッド（$\ell\_{\infty}$球）に分割し、最適化アルゴリズムを各クエリ$x\_k$に対して$f(x\_k)=0$を返してシミュレートします。そうすると、$N < \lfloor L/2\varepsilon \rfloor^n$回の試行では、少なくとも１つ以上、アルゴリズムのクエリが入らないグリッドが存在します。そのようなグリッドを一つとり、その中心を$x^\*$とし、関数を
\begin{align}
  f(x) = \min \\{0, L \lVert x - x^\* \rVert\_{(\infty)} - \varepsilon\\}.
\end{align}
と定めることで、$\lfloor L/2\varepsilon \rfloor^n$未満の反復では$\varepsilon$-近似解を得ることのできない関数を作り、反復数の下界が$\lfloor L/2\varepsilon \rfloor^n$であると証明しました。

上の証明でキモとなる操作の一つは、実行可能領域$B\_n$を小さなノルム球で分割することでした。$\ell_{2}$ノルムの場合は、直感的にはこのような分割を行えなさそうであるため、同様の方法では証明ができなさそうに思えます。しかし、よく考えると、$B\_n$を小さなノルム球で「分割」する必要はなく、小さなノルム球をいくつ「詰め込めるか」さえわかれば良いということがわかります：詰め込んだノルム球の数より少ない反復数の場合、クエリが入らないノルム球が存在するので、そのようなノルム球の中心に最適解を配置することで下界を導出することができます。すなわち、以下が成り立ちます。

補題2.1
: $d(x, y) = \lVert x - y \rVert\_2$、$\varepsilon < L/2$とする。このとき、前述の最適化問題における各0階の最適化アルゴリズムに対して、精度$\varepsilon$の解を得るために、少なくとも$\mathcal{P}(B\_n, d, 2\varepsilon/L)$回のオラクル呼び出しを必要とする関数$f$が存在する。

証明
: $B\_n$の$2\varepsilon/L$-separated $\mathcal{N} \subseteq B\_n$を一つとる。$N < |\mathcal{N}|$とし、最適化アルゴリズムを、各クエリ$x_k$（$0 \le k < N$）に対して$f(x_k)=0$を返すことで$N$回シミュレートする。$\mathcal{N}$の各点は、定義より、互いに距離が$2\varepsilon/L$より大きい。したがって、各$x_k$について、$\mathcal{N}$の点で$x_k$との距離が$\varepsilon/L$以下であるような点は高々1つである：$|\\{y \in \mathcal{N}: d(x_k, y) \le \varepsilon/L\\}| \le 1$。ゆえに、（$N<|\mathcal{N}|$より）すべての$x_k$について、距離が$\varepsilon/L$より大きい$y \in \mathcal{N}$が少なくとも1つ以上存在する：$\\{y \in \mathcal{N}: d(x_k, y) > \varepsilon/L, 0\le k < N\\} \neq \emptyset$。そのような点の一つを$x^\* $とし、$f$を
\begin{align}
    f(x) = \min \left\\{0, L \lVert x - x^\* \rVert\_{2} - \varepsilon\right\\}.
\end{align}
とすると、この$f$は$L$-リプシッツ連続性を満たし、$f(x^\* )=-\varepsilon$であり、各$f(x_k)=0$でシミュレーションに矛盾せず、最適化アルゴリズムは$N < |\mathcal{N}|$回のクエリでは$\varepsilon$-近似解を得ることができない。$\mathcal{N}$は任意の$2\varepsilon/L$-separatedであり、$2\varepsilon/L$-separatedの最大濃度は$\mathcal{P}(B\_n, d, 2\varepsilon/L)$であることから、主張を得る。

下界を得ることができましたが、補題2.1もやはり単に充填数が下界になると主張しているだけで、これだけではよくわかりません。ここで、[前回の記事]({{< ref "/posts/covering-packing.md" >}})で示した被覆数の上界・下界（系4.2.13）と被覆数と充填数の等価性（補題4.2.8）より、以下の主張を得ます。

定理2.2
: $d(x, y) = \lVert x - y \rVert_2$、$\varepsilon < L/2$とする。このとき、前述の最適化問題における各0階の最適化アルゴリズムに対して、精度$\varepsilon$の解を得るために、少なくとも$(L/2\varepsilon)^{n}$回のオラクル呼び出しを必要とする関数$f$が存在する。

証明
: 系4.2.13と補題4.2.8より、
\begin{align}
    (L/2\varepsilon)^n \le \mathcal{N}(B\_n, d, 2\varepsilon/L) \le \mathcal{P}(B\_n, d, 2\varepsilon/L)
\end{align}
であり、これを補題2.1に適用することで主張を得る。

# ランダムフーリエ特徴の誤差の評価
ランダムフーリエ特徴[2]について簡単に説明します。ランダムフーリエ特徴[2]は、カーネル法、特に、平行移動不変なカーネル関数を用いるカーネル法の近似アルゴリズムの一つです。平行移動不変なカーネル関数とは、二つの入力$\pmb{x}, \pmb{y} \in \mathbb{R}^d$の差$\pmb{x}-\pmb{y}$の一変数関数として書けるようなカーネル関数のことで、例えばRBFカーネル$k(\pmb{x}, \pmb{y}) = \exp (-\lVert \pmb{x}-\pmb{y}\rVert\_2^2/ (2\sigma^2))$等が該当します（以降、平行移動不変な二変数関数を$\pmb{x}-\pmb{y}$の一変数関数とみなし、$k(\pmb{x}-\pmb{y})$のように書くことがあります）。最大値が1にスケーリングされた平行移動不変なカーネル関数$k:\mathbb{R}^d\times\mathbb{R}^d\to\mathbb{R}$に対する、$2D$次元のランダムフーリエ特徴写像$z: \mathbb{R}^d \to \mathbb{R}^{2D}$は以下で定義されます：
\begin{align}
    z(\pmb{x}) := \frac{1}{\sqrt{D}}\begin{pmatrix}
        \cos \left(\pmb{w}\_1^\top \pmb{x}\right) \\\\\\
        \sin \left(\pmb{w}\_1^\top \pmb{x}\right) \\\\\\
        \vdots \\\\\\
        \cos \left(\pmb{w}\_D^\top \pmb{x}\right) \\\\\\
        \sin \left(\pmb{w}\_D^\top \pmb{x}\right)
    \end{pmatrix}
    \label{eq:random_fourier_feature}
\end{align}
ここで、$\pmb{w}\_{i} \in \mathbb{R}^d, \ i \in [D]$は各要素がカーネル関数$k$によって定まる適切な分布からサンプリングされたベクトルです。$D$はユーザが定めるパラメータで、$D$はモンテカルロサンプリングにおけるサンプリング数のようなものだと思えばよいです（大きいほど近似精度が良くなります）。$\pmb{w}\_{i}$の分布は近似したいカーネル関数に依存しますが、よく知られたものだと、例えばRBFカーネル$k(\pmb{x}, \pmb{y})=\exp (-\lVert \pmb{x}-\pmb{y}\rVert_2^2/2\sigma^2)$のときはガウス分布$\mathcal{N}(0, \sigma^2)$になります。

上述したランダムフーリエ特徴はカーネル関数$k$を内積の意味で近似します。より正確に言うと、ランダムフーリエ特徴の内積は、元のカーネル関数の不偏推定量になっています：
\begin{align}
  \mathbb{E}\_{\pmb{W}}[z(\pmb{x})^\top z(\pmb{y})] = k(\pmb{x}, \pmb{y}) \ \forall \pmb{x}, \pmb{y} \in \mathbb{R}^d.
\end{align}
したがって、カーネル法を使う代わりに、このランダムフーリエ特徴を新たな特徴量とした線形モデルを用いることで、カーネル法と同等の精度を達成しつつ、カーネル法の持つ次元の呪いならぬサンプル数の呪いから脱却しよう、というのがランダムフーリエ特徴のアイデアです。もちろん、サンプル数の呪いから逃れた代わりに今度は次元の呪い問題に直面するわけですが、$D$はそう大きくなくともうまく動くケースが多いです（ただし、元の次元$d$よりは大きくなければならないことが多いです）。カーネル関数によって誘導される超高次元空間の特徴ベクトルに対する次元削減を、超高次元空間の座標を介さずに・学習などもせずランダムに行う方法、と考えるとわかりやすいかもしれません。

## ランダムフーリエ特徴の誤差のバウンド
ランダムフーリエ特徴同士の内積の期待値は元のカーネル関数の値と等しいですが、あくまで期待値が等しいというだけなので、有限の$D$では誤差が生じます。そうなると当然、その誤差がどれくらいのものであるのか、というのが気になってきます。幸い、Hoeffdingの不等式を用いることで以下の集中不等式を得ることができます（有界な確率変数に対するHoeffdingの不等式を用いるだけなので証明は省略）。

補題3.1
: $k:\mathbb{R}^d\times\mathbb{R}^d \to \mathbb{R}$を最大値が1にスケーリングされた平行移動不変なカーネル関数、$z: \mathbb{R}^d \to \mathbb{R}^{2D}$を式\eqref{eq:random_fourier_feature}で定義された$k$の$2D$次元のランダムフーリエ特徴とする。このとき、任意の$\pmb{x}, \pmb{y} \in \mathbb{R}^d$について、
\begin{align}
    p\left(\lvert z(\pmb{x})^\top z(\pmb{y}) - k(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \le 2 \exp \left(-\frac{D\varepsilon^2}{4}\right).
    \label{eq:random_fourier_error}
\end{align}

## ランダムフーリエ特徴の誤差の一様なバウンド
補題3.1で示した不等式\eqref{eq:random_fourier_error}は、与えられた任意の一つのペアの誤差が$\varepsilon$以上になる確率を抑えたものでした。しかし、カーネル関数とその誤差を評価したいのは一つのペアだけではないでしょう。そこで、最悪ケース、すなわち誤差の上限に関する集中不等式
\begin{align}
    p\left(\sup_{\pmb{x}, \pmb{y} \in \mathcal{M} \subset \mathbb{R}^d} \lvert z(\pmb{x})^\top z(\pmb{y}) - k(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \le \ ?
\end{align}
を考えることにします。ここで、$\mathcal{M} \subseteq \mathbb{R}^d$は特徴ベクトルの集合・定義域です。もし$\mathcal{M}$が有限集合であるならば、誤差の最大値は誤差の和より小さい（誤差の最大値が$\varepsilon$以上ならば、和も$\varepsilon$以上である）ことと、Union Boundにより、とりあえずは
\begin{align}
    p\left(\max_{\pmb{x}, \pmb{y} \in \mathcal{M}} \lvert z(\pmb{x})^\top z(\pmb{y}) - k(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right)  & \le p\left(\sum\_{\pmb{x}, \pmb{y} \in \mathcal{M}} \lvert z(\pmb{x})^\top z(\pmb{y}) - k(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \\\\\\
    & \le \sum\_{\pmb{x}, \pmb{y} \in \mathcal{M}}  p\left(\lvert z(\pmb{x})^\top z(\pmb{y}) - k(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \\\\\\
    & \le 2\lvert \mathcal{M} \rvert^2 \exp \left(-\frac{D\varepsilon^2}{4}\right)
\end{align}
と抑えることができます。しかし、$\mathcal{M}$は非可算無限集合（例えば、ノルムの制限された集合）と仮定されることが多いと考えられ、その場合は上のような導出はできません。そこで、$\varepsilon$-netを利用します：$\mathcal{M}$に対する$\varepsilon$-netを構成し、$\varepsilon$-netを$\mathcal{M}$の代表点の集合として考えることで、$\mathcal{M}$というヨクワカラン集合上の問題を、「有限個」の小さな球上の解きやすい問題に落とし込みます。

定理3.2
: $k:\mathbb{R}^d\times\mathbb{R}^d \to \mathbb{R}$は、連続、平行移動不変、最大値$k(0)=1$、$\nabla^2 k(0)$が存在する（半正定値な）カーネル関数とする。$z: \mathbb{R}^d \to \mathbb{R}^{2D}$を式\eqref{eq:random_fourier_feature}で定義された$k$の$2D$次元のランダムフーリエ特徴、$\mathcal{M}$を$\mathbb{R}^d$のコンパクト部分集合とし、
\begin{align}
    s(\pmb{x}, \pmb{y}) &:= z(\pmb{x})^{\top}z(\pmb{y}),\\\\\\
    f(\pmb{x}, \pmb{y}) &:= s(\pmb{x}, \pmb{y}) - k(\pmb{x}, \pmb{y})
\end{align}
とする。このとき、$\sigma_p^2 = \mathbb{E}[w^2]$、$\mathrm{diam}(\mathcal{M})$は$\mathcal{M}$の直径であり、$\sigma_p$、$\mathrm{diam}(\mathcal{M})$、$D$、$\varepsilon$が条件
\begin{align}
    \frac{\sigma_p \mathrm{diam}(\mathcal{M})}{\varepsilon} \ge 1, \ D \ge \frac{32 d}{\varepsilon^2}
    \label{eq:random_fourier_condition}
\end{align}
を満たすならば、
\begin{align}
    p\left(\sup_{\pmb{x}, \pmb{y} \in \mathcal{M} \subset \mathbb{R}^d} \lvert f(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \le 72 \left(\frac{\mathrm{diam}(\mathcal{M})\sigma\_p}{\varepsilon}\right)^2 \exp \left(-\frac{D\varepsilon^2}{8(d+2)}\right)
    \label{eq:random_fourier_error_uniform_bound}
\end{align}
が成り立つ。

証明
: $k$は定義より$\Delta=\pmb{x}-\pmb{y}$の一変数関数として書くことができる。$s$は
\begin{align}
    s(\pmb{x}, \pmb{y}) &= \frac{1}{D}\sum\_{j=1}^{D} \left(\cos \left(\pmb{w}\_j^\top \pmb{x}\right) \cdot \cos \left(\pmb{w}\_j^\top \pmb{y}\right) + \sin \left(\pmb{w}\_j^\top \pmb{x}\right) \cdot \sin \left(\pmb{w}\_j^\top \pmb{y}\right)\right) \\\\\\
    &= \frac{1}{D}\sum\_{j=1}^{D} \cos \left(\pmb{w}\_j^\top (\pmb{x}-\pmb{y})\right)
\end{align}
と変形できるため、$s$も平行移動不変、ゆえに$f$も平行移動不変である。したがって、これ以降は$\mathcal{M}\_{\Delta} := \\{\pmb{x}-\pmb{y} : \pmb{x}, \pmb{y} \in \mathcal{M}\\}$上で誤差$f$を考える。

: $\mathcal{M}$がコンパクトであるという仮定より、$\mathcal{M}\_{\Delta}$もまたコンパクトである。さらに、$\mathcal{M}\_{\Delta}$は中心$\pmb{0}$、半径$\mathrm{diam}(\mathcal{M})$のユークリッド球に覆われる：$\mathcal{M}\_{\Delta} \subset B_2^d(\pmb{0}, \mathrm{diam}(\mathcal{M}))$。ゆえに、[前回の記事]({{< ref "/posts/covering-packing.md" >}})で示したユークリッド球の被覆数の上界（系4.2.13）より、$\mathcal{M}\_{\Delta}$の$r$-被覆数$\mathcal{N}(\mathcal{M}\_{\Delta}, r)$について
\begin{align}
    \mathcal{N}(\mathcal{M}\_{\Delta}, r) \le \mathcal{N}(B_2^d(\pmb{0}, \mathrm{diam}(\mathcal{M})), r) \le \left(\frac{\mathrm{diam}(\mathcal{M})}{r} + 2\right)^{d}\ \forall r > 0
    \label{eq:covering_number_upper_bound}
\end{align}
が成り立つ。

: $\mathcal{N} = \\{\Delta\_{i}\\}\_{i=1}^{T} \subset \mathcal{M}\_{\Delta}$を$\mathcal{M}\_{\Delta}$の$r$-netの一つとする。また、$L\_f > 0$を、$f$の（$\mathcal{M}\_{\Delta}$での）リプシッツ定数であるとする。このとき、$\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert$について
\begin{align}
    \sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert &\le \max\_{i \in [T]} \max\_{\Delta \in B\_{2}^d(\Delta\_i, r)} |f(\Delta)| \label{eq:supf_le1} \\\\\\
    & \le \max\_{i \in [T]} \max\_{\Delta \in B\_{2}^d(\Delta\_i, r)} \left(L\_f \lVert \Delta\_i - \Delta \rVert\_2 + \lvert f(\Delta\_i) \rvert \right) \label{eq:supf_le2}\\\\\\
    &\le L\_f \cdot r + \max\_{i \in [T]} |f(\Delta\_i)|. \label{eq:supf_le3}
\end{align}
が成り立つ。ただし、一つ目の不等式\eqref{eq:supf_le1}では$\mathcal{N}$が$r$-netである（すなわち、$\\{\Delta_i \\}\_{i=1}^{T}$を中心とする半径$r$の球の和集合が$\mathcal{M}\_{\Delta}$を覆っている）こと、二つ目の不等式\eqref{eq:supf_le2}では$f$の$\mathcal{M}$でのリプシッツ連続性、最後の不等式\eqref{eq:supf_le3}では再び$r$-netであることを利用した。不等式\eqref{eq:supf_le3}より、$\max\_{i \in [T]}\lvert f(\Delta_i) \rvert \le \frac{\varepsilon}{2}$かつ$L_f \le \varepsilon / (2r)$ならば$\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert \le \varepsilon$となることがわかる。それぞれの確率の上限を求める。まず、Union Boundと補題3.1より、
\begin{align}
    p\left(\max\_{i \in [T]}\lvert f(\Delta_i) \rvert \ge \frac{\varepsilon}{2}\right) \le 2T\exp \left(-\frac{D\varepsilon^2}{16}\right) \label{eq:nat_max_error_bound}
\end{align}
を得る。Markovの不等式より、
\begin{align}
    p\left(L\_f \ge \frac{\varepsilon}{2r} \right) = p\left(L\_f^2 \ge \left(\frac{\varepsilon}{2r}\right)^2 \right) \le \mathbb{E}\left[L\_f^2\right ] \cdot \left(\frac{2r}{\varepsilon}\right)^2
\end{align}
であり、さらに$\mathbb{E}[L_f^2]$は以下のように計算できる（この計算はやや面倒であるのと、主として扱いたい部分でもないので省略、詳細は[3]を参照）：
\begin{align}
    \mathbb{E}[L_f^2] \le \mathbb{E}[w^2] = \sigma_p^2.
\end{align}
それゆえ、
\begin{align}
    p\left(L\_f \ge \frac{\varepsilon}{2r} \right) \le \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2 \label{eq:lf_bound}
\end{align}
が成り立つ。不等式\eqref{eq:lf_bound}と\eqref{eq:nat_max_error_bound}を用いることで、
\begin{align}
    p\left(\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert \le \varepsilon\right) &\ge 1 - 2 T \exp \left(-\frac{D\varepsilon^2}{16}\right) - \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2 \\\\\\
    \to p\left(\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert \ge \varepsilon\right) & \le 2 T \exp \left(-\frac{D\varepsilon^2}{16}\right) + \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2
\end{align}
を得る。$\mathcal{N}$は任意の$r$-netであったから、被覆数の不等式\eqref{eq:covering_number_upper_bound}より、さらに
\begin{align}
    p\left(\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert \ge \varepsilon\right) \le 2 \left(\frac{2\mathrm{diam}(\mathcal{M})}{r} + 1\right)^{d} \exp \left(-\frac{D\varepsilon^2}{16}\right) + \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2
\end{align}
を得る。$r < \mathrm{diam}(\mathcal{M})$とすると、$2\mathrm{diam}(\mathcal{M})/r + 1 < 3\mathrm{diam}(\mathcal{M})/r$より、さらに
\begin{align}
    p\left(\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert \ge \varepsilon\right) \le 2 \left(\frac{3\mathrm{diam}(\mathcal{M})}{r}\right)^{d} \exp \left(-\frac{D\varepsilon^2}{16}\right) + \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2
    \label{eq:random_fourier_error_uniform_bound_simple}
\end{align}
を得る。ここで、$c\_1 r^{-d} + c\_2 r^2$は、$r=(c\_1 / c\_2)^{1/(d+2)}$とすると、$c\_1 r^{-d} + c\_2 r^2=2 c\_2 ^{d/(d+2)} c_1 ^{2/(d+2)}$と書ける。これを\eqref{eq:random_fourier_error_uniform_bound_simple}に適用すると、$c_1$と$c_2$はそれぞれ
\begin{align}
    c_1=2 \left(3\mathrm{diam}(\mathcal{M})\right)^{d}\exp\left(-\frac{D\varepsilon^2}{16}\right), \ c_2=\left(\frac{2\sigma_p}{\varepsilon}\right)^2
\end{align}
であるから、
\begin{align}
    r = \left(2 \left(3\mathrm{diam}(\mathcal{M})\right)^{d}\exp\left(-\frac{D\varepsilon^2}{16}\right) \cdot \left(\frac{\varepsilon}{2\sigma_p}\right)^2 \right)^{1/(d+2)}
    \label{eq:appropriate_r}
\end{align}
とすると、
\begin{align}
    p\left(\sup\_{\Delta \in \mathcal{M}\_{\Delta}} \lvert f(\Delta) \rvert > \varepsilon\right) &\le 2 \left(\frac{3\mathrm{diam}(\mathcal{M})}{r}\right)^{d} \exp \left(-\frac{D\varepsilon^2}{16}\right) + \left(\frac{2\sigma\_p r}{\varepsilon}\right)^2 \\\\\\
    &= 2\cdot 2^{\frac{2+2d}{d+2}}\cdot 3^\frac{2d}{d+2}\exp \left(-\frac{D\varepsilon^2}{8(d+2)}\right)\left(\frac{\mathrm{diam}(\mathcal{M})\sigma\_p}{\varepsilon}\right)^{\frac{2d}{d+2}} \\\\\\
    &\le 72 \left(\frac{\mathrm{diam}(\mathcal{M})\sigma\_p}{\varepsilon}\right)^2 \exp \left(-\frac{D\varepsilon^2}{8(d+2)}\right)
\end{align}
が成り立つ。これはまさしく式\eqref{eq:random_fourier_error_uniform_bound}である。ただし、最後の不等式では\eqref{eq:random_fourier_condition}の1つ目を利用した。また、式\eqref{eq:appropriate_r}で定められた$r$は$r < \mathrm{diam}(\mathcal{M})$の条件を満たす：
\begin{align}
    r &= \left(2 \left(3\mathrm{diam}(\mathcal{M})\right)^{d}\exp\left(-\frac{D\varepsilon^2}{16}\right) \cdot \left(\frac{\varepsilon}{2\sigma_p}\right)^2 \right)^{1/(d+2)}\\\\\\
    &\le \left(2 \left(3\mathrm{diam}(\mathcal{M})\right)^{d}\exp\left(-2d\right) \cdot \left(\frac{\varepsilon}{2\sigma_p}\right)^2 \right)^{1/(d+2)} \\\\\\
    &\le \mathrm{diam}(\mathcal{M}) \cdot \left(\frac{1}{2}\left(\frac{3}{\exp (2)}\right)^d\cdot \left(\frac{\varepsilon}{\mathrm{diam}(\mathcal{M})\sigma_p}\right)^2 \right)^{1/(d+2)}\\\\\\
    &< \mathrm{diam}(\mathcal{M}).
\end{align}
以上で主張を得た。

ちなみに、元の論文[2]では、被覆数に関する次の不等式[4, Proposition 5]を利用します：
\begin{align}
    \mathcal{N}(B_2^d(\pmb{0}, \mathrm{diam}(\mathcal{M})), r) \le \left(\frac{4\mathrm{diam}(\mathcal{M})}{r}\right)^d.
    \label{eq:covering_number_upper_bound_original}
\end{align}
証明を書くの画面だったのと、せっかくなので前回導出した不等式を使おうと考え、上で示した証明では使いませんでした。この不等式\eqref{eq:covering_number_upper_bound_original}のもとでは、\eqref{eq:random_fourier_condition}の$D$に関する条件なしに以下を得ます：
\begin{align}
    p\left(\sup_{\pmb{x}, \pmb{y} \in \mathcal{M} \subset \mathbb{R}^d} \lvert f(\pmb{x}, \pmb{y})\rvert \ge \varepsilon \right) \le 128 \left(\frac{\mathrm{diam}(\mathcal{M})\sigma_p}{\varepsilon}\right)^2 \exp \left(-\frac{D\varepsilon^2}{8(d+2)}\right)
    \label{eq:random_fourier_error_uniform_bound_original}
\end{align}
\eqref{eq:random_fourier_error_uniform_bound}と\eqref{eq:random_fourier_error_uniform_bound_original}では定数倍が変わっただけなので、仮定がなくなっている分、被覆数の不等式としては\eqref{eq:covering_number_upper_bound_original}を利用したほうが良さそうに思えますが、実は、案外そうでもありません。$D$の条件\eqref{eq:random_fourier_condition}は、式\eqref{eq:appropriate_r}で定めた$r$が$r < \mathrm{diam}(\mathcal{M})$の条件を満たすように仮定したものです。この条件を満たさない場合、すなわち$r \ge \mathrm{diam}(\mathcal{M})$の場合でも元論文の\eqref{eq:random_fourier_error_uniform_bound_original}は成り立つわけですが、そもそもこの条件を満たさない場合、\eqref{eq:covering_number_upper_bound}や\eqref{eq:covering_number_upper_bound_original}は非常にガバガバなバウンドになります（$r>\mathrm{diam}(\mathcal{M})$のとき、$\\{\pmb{0}\\}$が自明に$\mathcal{M}\_{\Delta}$の$r$-netになる、すなわち被覆数は$1$）。結果として、元論文のバウンド\eqref{eq:random_fourier_error_uniform_bound_original}は例えば「誤差の最大値が$\varepsilon$以上になる確率は1000以下」といったように無意味なものとなります。この意味で、大差はありません。

# おわりに
本記事では、被覆数と充填数の簡単な応用を紹介しました。

- 誤り訂正符号: 冗長性をもたせることで誤りの訂正を可能にするが、一体どの程度の冗長性をもたせればよいのかを見積もるのに充填数を利用しました。$2r$-separatedの各要素を符号語として利用することで、必要なbit数を充填数によって条件つけることができます。
- 0階の最適化アルゴリズム:$\ell_{\infty}$ノルム球上で考える場合は、球をグリッドに区切ることで必要な反復数の下界を導出しましたが、$\ell_2$ノルム球上で考える場合は、充填数によって必要な反復数の下界を導出することができます。
- ランダムフーリエ特徴：誤差の一様な上界を導出するのに被覆数を利用しました。（非可算）無限集合に対しては雑なUnionバウンドは意味を持ちませんが、netを代表点とすることで、Unionバウンドを取る操作を意味のある行為にし、それによって一様な上界を導出することができます。

# 参考文献
[1] R. Vershynin. High dimensional probability. An introduction with applications in Data Science. Cambridge University Press, 2018.

[2] A. Rahimi and B. Recht. Random Features for Large-Scale Kernel Machines. In NeurIPS, pp. 1177--1184, 2007.

[3] D. J. Sutherland and J. Schneider. On the error of random fourier features. In UAI, pp.  862–-871, 2015.

[4] F. Cucker and S. Smale. On the mathematical foundations of learning. Bull. Amer. Soc., 39:1–49, 2001.

# 日記
本記事の11月中の投稿を犠牲にすることでプレイしたダンガンロンパはとても面白かったです。私はミステリがそれなりに好きなのですが、ダンガンロンパで発生した事件はどれも、事件発生前や事件発生直後の予想からもう一段ステップ複雑で、とても良い体験ができました。また、キャラゲーとしても個人的にはとても満足でした。チャプター1の終わりに、苗木くんが、舞園さんの口癖を受けたセリフを言うシーンがとても好きです。人工知能技術の例としてSVMが挙げられていたのも趣深かったです。また、先日たまたまコインランドリーを利用したのですが、掛け布団が激しく回っている様を見て某バターのオシオキが脳裏によぎりました。ダンガンロンパをプレイ済みの皆様も是非、ドラム式洗濯機をご利用の際は思い出してください。

---
title: "LaTeX Test"
date: 2021-10-16 03:00:00 +0900
draft: true
tags:
    - test
categories:
    - tech
keywords:
    - MathJax
mathjax: true
---

# はじめに
Github Pagesを用いて、テックっぽいことや機械学習とその周辺分野に関するアウトプットを行うことにした。
Github Pagesを選んだのは、自由度と手軽さのバランスが良さそうだったからなわけだが、とはいえ自分でゴリゴリCSSやらHTMLやらを書くのは気乗りしない（しセンスもない）ので、[Hugo](https://gohugo.io/)を使って整備をすることにした。
HugoはGo言語で書かれた静的サイトジェネレータで、高速なのが売りっぽい。
[テーマも様々提供されていて](https://themes.gohugo.io/)、どれもこれも質が高い。
パーッと眺めてみて、なんとなく[Cactus](https://themes.gohugo.io/themes/hugo-theme-cactus/)というテーマが気に入りそれを利用することにした。
Github Pages及びHugoの導入自体は非常に簡単で、公式の[quick-start](https://gohugo.io/getting-started/quick-start/)と[hosting-on-github](https://gohugo.io/hosting-and-deployment/hosting-on-github/)に従えば良い。


# MathJaxの導入
記事を書くにあたって、数式の記載は避けられない。
このような場合、[MathJax](https://www.mathjax.org/)が定番だと思っていたが、ちょっと調べて見た感じだと、どうやら[KaTeX](https://katex.org/)というのが最近は良いらしい。
が、CactusでMathJaxが標準でサポートされているので、今回はそのままMathJaxを使うことにする。
速度に不満が出てきたり、Hugoに興味がで場合にKaTeXへの以降を考えたい。

CactusでのMathJaxの利用は簡単で、front matterに`mathjax: true`と記載すれば良い。
以下、簡単なテスト。

## inline数式
`$`で囲めば良い。
$a$, $\boldsymbol{a}$, $\lVert \boldsymbol{x}^\top \rVert^2$ $\sum_{i=1}^N x_i$

## 別行立て
`$$`で囲めば良い。
$$
\exp (a_i) + \log \left [\frac{b_i}{c_i} + d_i \right] + \lambda
$$

`align`も使える。[改行はバックスラッシュ6つで行う](https://github.com/wowchemy/wowchemy-hugo-themes/issues/1480)。
\begin{align}
  \log abc &= \log ab + \log c  \\\\\\ 
  &=\log a + \log b + \log c
\end{align}
上の数式は以下に対応する。
```
\begin{align}
  \log abc &= \log ab + \log c  \\\\\\ 
  &=\log a + \log b + \log c
\end{align}
```

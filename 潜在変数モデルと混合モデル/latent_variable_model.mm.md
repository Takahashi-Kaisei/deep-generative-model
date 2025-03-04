# 潜在変数モデルと混合モデル
## 問題
複数のコインK個を投げて，一枚を選ぶ．という問題．しかし，どのコインを投げたかはわからない．

### 潜在変数
どのコインであるか．というのが潜在変数．

潜在変数を$\boldsymbol{z}$とすると，ワンホットベクトルとして，以下のように表すことができる．
$
\boldsymbol{z} = \begin{pmatrix} z_1 \\ z_2 \\ \vdots \\ z_k \end{pmatrix} \in \{0, 1\}^K
$

#### 潜在変数が従う確率分布
今回，$\boldsymbol{z}$が従う分布をカテゴリ分布とする．

k番目が選ばれる確率は$\pi_k$　($\sum_{k=1}^K \pi_k = 1, \pi_k \in [0, 1]$)とすると，カテゴリ分布の確率質量関数は，
$
p_{\boldsymbol{\pi}}(\boldsymbol{z}) = \prod_{k=1}^K \pi_k^{z_k} \equiv \text{Cat}(\boldsymbol{z};\boldsymbol{\pi})
$

$\boldsymbol{\pi}$は確率って言ってるので，総和は1になる．

確率質量関数は，単純に，$z_k$が1のとき，$\pi_k$が返ってくるだけ．なぜなら，$z_k$はワンホットベクトルだから．$z_k=0$のとき，0乗で1になるから．
要は，$p_{\boldsymbol{\pi}}(\boldsymbol{z})$は，選ばれたコインの確率が返ってくるだけね．

パラメータ$\boldsymbol{\pi}$は，コインの選ばれやすさを表す．

$\boldsymbol{\pi} = \begin{pmatrix} \pi_1 \\ \pi_2 \\ \vdots \\ \pi_K \end{pmatrix} \in \mathbb{R}^K$
ここに，$K$種類のコインそれぞれに対しての選ばれやすさ，確率が入っている．
$\boldsymbol{\pi}$は，それぞれのコインの選ばれやすさを表すベクトル．

### 条件付き分布

ベルヌーイ分布のパラメータがコインごとに設定されているとする．
$\mu_k$が$k$番目のコインが$x=1$となる確率を表す．表になる確率ってこと．
単純にベルヌーイ分布のパラメータとして，コインが表をとる確率を$\mu_k$で与えているだけだね．

んで，潜在変数$\boldsymbol{z}$を選んだ下で，観測変数$x$の条件付き分布は，

$
p_{\boldsymbol{\mu}}(x|\boldsymbol{z}) = \prod_{k=1}^K Bern(x;\mu_k)^{z_k}
= \prod_{k=1}^K \{\mu_k^{x} (1-\mu_k)^{1-x}\}^{z_k}
$

$p_{\boldsymbol{\mu}}(x|\boldsymbol{z})$は，確率分布全体は，$\mu$というパラメータにしたがっていることを表す．
今回の問題設定では，$\mu$は，コインの表が出る確率を表す．変数$x$はパラメータ$\mu$に従っている．
だがしかし，注意したいのは，$z$は，どのコインを選んだかを表す．つまりコインの表が出る確率$\mu$には従わない．
あくまでも，$p_{\boldsymbol{\mu}}(x|\boldsymbol{z})$は確率分布全体が，$\mu$にしたがっていることを表す．

$\prod_{k=1}^K Bern(x;\mu_k)^{z_k}$は，コインそれぞれに対する，確率質量関数の積を表す．$\boldsymbol{z}$は，どのコインが選ばれたかのベクトルが入っている．
つまり，選ばれたコインのベルヌーイ分布のみが返ってくるということ．(選ばれなかったとすると，$z_k=0$なので，全体として1になるからね．)

$\{\mu_k^{x} (1-\mu_k)^{1-x}\}$は，ベルヌーイ分布を展開したもの．$z_k$が指数につくので，選ばれたコインの場合，つまり$z_k=1$の場合を考える．
表が出た場合，$x=1$になるので，つまり，表が出れば$\mu_k$が採用される．
裏が出た場合，$x=0$になるので，つまり，裏が出れば$1-\mu_k$が採用される．

最終的には，選ばれたコインの表が出る確率を計算してるってこと．
(起きた事象については，潜在変数$z$の確率分布を仮定していないので，まだわからないよ．)

最後にちょいまとめると，
この条件付き分布は，今回の問題．「複数のコインK個を投げて，一枚を選ぶ．という問題．しかし，どのコインを投げたかはわからない．」をうまく表現できている．
$\boldsymbol{z}$はワンホットベクトルで，選ばれたコインのみが1になるので，選ばれたコインの確率を取り出せている．
(選ばれなかったコインは，0乗になり，ただ1になるだけだから．無視できる．)
その後，コインの裏表によって，$\mu_k$が採用されるのか(裏である確率)，$1-\mu_k$が採用されるのか(表である確率)が選ばれる．
ちゃんと問題をモデル化できていることがわかる．

### 生成過程

観測する観測データ集合$X=\{x_i\}_{i=1}^N$の生成モデルの生成過程．
このときの観測データ$x$は，$i$一つずつみていったとき，ベクトルではない．ただのスカラー値．裏表なので，0か1．

すべての$i = 1, ... N$について，

- 事前分布から潜在変数をサンプリング．$K$個のコインのうち一つを選ぶ．

$
\boldsymbol{z}_i \sim \boldsymbol{p}_{\pi}(\boldsymbol{z}) = \text{Cat}(\boldsymbol{z};\boldsymbol{\pi})
$

潜在変数の，$i$番目のデータに対する選ばれたコインの確率がサンプリングされる．
$\boldsymbol{z}_i$は， $\boldsymbol{p}_{\pi}(\boldsymbol{z})$の確率モデルから，$K$個のコインのうち一つを選ぶ．ということ．

それを簡略化すると，$\text{Cat}(\boldsymbol{z};\boldsymbol{\pi})$

- 選択したコインを振って，裏表を観測する．

$
x_i \sim p_{\boldsymbol{\mu}}(x|\boldsymbol{z} = \boldsymbol{z}_i) = Bern(x;\mu_{k})
$
ただし，$k$は，$\boldsymbol{z}_i$が表すカテゴリ，つまり，選ばれたコインを示す．($k = \arg \max_k z_{ik}$)


### 混合ベルヌーイモデル

問題の例で表現すると，$K$個のベルヌーイ分布があるということ．

- 生成モデルの同時分布

$
p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(\boldsymbol{z}, \boldsymbol{x})
= p_{\boldsymbol{\pi}}(\boldsymbol{z}) p_{\boldsymbol{\mu}}(\boldsymbol{x}|\boldsymbol{z})
= \prod_{k=1}^K \{\pi_k \mu_k^{x}(1-\mu_k)^{1-x}\}^{z_k}
$



$p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(\boldsymbol{z}, \boldsymbol{x})$はパラメータ$\boldsymbol{\pi}，\boldsymbol{\mu}$のもとで，$\boldsymbol{z}$と$\boldsymbol{x}$が同時に観測される同時分布ということ．

$p_{\boldsymbol{\pi}}(\boldsymbol{z})$は潜在変数という確率分布&次の条件付き分布の条件で，$p_{\boldsymbol{\mu}}(\boldsymbol{x}|\boldsymbol{z})$は潜在変数という確率分布が条件として与えられた条件付き分布である．
条件と，その条件の元での条件付き分布との積は，同時分布を表すということがわかる．

$\prod_{k=1}^K \{\pi_k \mu_k^{x}(1-\mu_k)^{1-x}\}^{z_k}$はそれを展開したもの，
これはそのまま，その事象が起きた確率を表している．
具体的には，$k$番目のコインが選ばれて，そのコインが，表or裏であった確率を表している．

- 周辺化

!!!ここあとで確認
同時分布が得られたのは良いが，$\boldsymbol{z}$は実際には得られない(わからない)ので，$\boldsymbol{z}$について総和を取る．(周辺化)
→実際には得られないので，周辺化($z$の値によらなくすることによって，現実の問題をしっかりモデル化する．)
要は$z_k$についてどれでも良くするってことね．

$$
\begin{align*}
p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(x) &= \sum_{k=1}^K p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(\boldsymbol{z}, \boldsymbol{x})\\
&=\sum_{k=1}^K p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(x, z_k = 1)\\
&=\sum_{\boldsymbol{z}} \prod_{k=1}^K \{\pi_k \mu_k^{x}(1-\mu_k)^{1-x}\}^{z_k}\\
&= \sum_{k=1}^{K}  \pi_k\mu_k^{x}(1-\mu_k)^{1-x}
\end{align*}
$$

$k$番目のコインがどれだけ選ばれやすいか($\pi_k$)，$k$番目のコインがどれだけ表が出やすいか($\mu_k$)を考えて，それの線形和をとっている．

周辺分布は，ベルヌーイ分布の線形和で表される．(混合ベルヌーイ分布)
確率分布の線形和で表されるモデルは混合モデルと呼ばれる．
潜在変数のパラメータ$\pi_k$が各カテゴリの重みに対応する．(混合係数)

### 対数周辺尤度をとる

先ほどの尤度(周辺尤度)，$p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(x)$を最大化するパラメータ$\boldsymbol{\pi}, \boldsymbol{\mu}$を求める．

そのために最尤推定する．
それを式に表す．

$$
\begin{align*}
\text{log}\ p_{\boldsymbol{\mu}, \boldsymbol{\pi}}(X) &= \sum_{i=1}^{N}  \text{log}\ p_{\boldsymbol{\mu}, \boldsymbol{\pi}}(x_i)\\
&= \sum_{i=1}^{N} \text{log}\ \sum_{k=1}^{K}  \pi_k\mu_k^{x_i}(1-\mu_k)^{1-x_i}\\
\end{align*}
$$

今回の問題の最尤推定では，各コインの表が出る確率のパラメータ$\mu_k$とコインを選ぶ確率のパラメータ$\pi_k$の二つを求めることになる．

ただし，対数の中に和があるからこの最大化は解析的に解けない．
log-sumの形は解析的に解けないことがわかっている．
ということで別の方法を考える．それがEMアルゴリズム．
詳細は[この資料に]('https://niltatsu.hatenablog.com/entry/em-algorithm1')

### EMアルゴリズム

先ほどの対数周辺尤度を式変形していく．
さっき変数に$X$をおいたけど，問題の単純化のために，ワンレコード$x$に戻して考える．
$\theta = \boldsymbol{\mu}, \boldsymbol{\pi}$
$$
\text{log}\ p_{\theta}(x) = \text{log}\ p_{\theta}(x)\sum_{\boldsymbol{z}}q(\boldsymbol{z})\\
$$
$\sum_{\boldsymbol{z}}q(\boldsymbol{z})=1$とすることで，1をかけても式全体は等価だから大丈夫になる．
$\text{log}\ p_{\theta}(x)$は，$\boldsymbol{z}$についての関数ではないので，$\sum$の中に入れることができる．
$$
= \text{log}\ p_{\theta}(x)\sum_{\boldsymbol{z}}q(\boldsymbol{z})\\
= \sum_{\boldsymbol{z}}q(\boldsymbol{z})\text{log}\ p_{\theta}(x)\\
$$
sum-logの形なので，ここで解析的に解けることが(多分)保証された．
次に，また1をかける操作を行う．
具体的には，
$$
= \sum_{\boldsymbol{z}}q(\boldsymbol{z})\text{log}\ p_{\theta}(x)\\
= \sum_{\boldsymbol{z}}q(\boldsymbol{z})\text{log}\ p_{\theta}(x)\\
$$




#### Eステップ

#### Mステップ

### 混合モデルに対するEMアルゴリズム
cv

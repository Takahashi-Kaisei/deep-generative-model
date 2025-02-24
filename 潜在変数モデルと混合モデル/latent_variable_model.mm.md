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
要は，$p_{\boldsymbol{\pi}}(\boldsymbol{z})$は，選ばれたコインの確率が返ってくるだけだね．

パラメータ$\boldsymbol{\pi}$は，コインの選ばれやすさを表す．
$\boldsymbol{\pi} = \begin{pmatrix} \pi_1 \\ \pi_2 \\ \vdots \\ \pi_K \end{pmatrix}$
ここに，$K$種類のコインそれぞれに対しての選ばれやすさ，確率が入っているんだね．

### 条件付き分布

ベルヌーイ分布のパラメータがコインごとに設定されているとする．
$\mu_k$が$k$番目のコインが$x=1$となる確率を表す．
単純にベルヌーイ分布のパラメータとして，コインが表をとる確率を$\mu_k$で与えているだけだね．

んで，潜在変数$\boldsymbol{z}$を選んだ下で，観測変数$x$の条件付き分布は，

$
p_{\boldsymbol{\mu}}(x|\boldsymbol{z}) = \prod_{k=1}^K Bern(x;\mu_k)^{z_k}
= \prod_{k=1}^K \mu_k^{x} \{(1-\mu_k)^{1-x}\}^{z_k}
$

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
p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(\boldsymbol{z}, \boldsymbol{x}) = p_{\boldsymbol{\pi}}(\boldsymbol{z}) p_{\boldsymbol{\mu}}(\boldsymbol{x}|\boldsymbol{z})
= \prod_{k=1}^K \mu_k^{x} \{\pi_k(1-\mu_k)^{1-x}\}^{z_k}
$

$p_{\boldsymbol{\pi}}(\boldsymbol{z})$は条件で，
$p_{\boldsymbol{\mu}}(\boldsymbol{x}|\boldsymbol{z})$は条件付き分布なので，その積は同時分布になる．

- 周辺化

!!!ここあとで確認
同時分布が得られたのは良いが，$\boldsymbol{z}$は実際には得られないので，$\boldsymbol{z}$について，総和を取る．(周辺化)
→実際には得られないので，周辺化($z$の値によらなくすることによって，現実の問題をしっかりモデル化する．)

$
p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(x) = \sum_{k=1}^K p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(\boldsymbol{z}, x)
=\sum_{k=1}^K p_{\boldsymbol{\pi}, \boldsymbol{\mu}}(x, z_k = 1)
= \sum_{k=1}^{K}  \pi_k\mu_k^{x}(1-\mu_k)^{1-x}
$

$k$番目のコインがどれだけ選ばれやすいか($\pi_k$)，$k$番目のコインがどれだけ表が出やすいか($\mu_k$)を考変えている．
それの線形和．
だから実際にコインを振った時に表の出やすさはこの式に集約される，

周辺分布は，ベルヌーイ分布の線形和で表される．(混合ベルヌーイ分布)
確率分布の線形和で表されるモデルは混合モデルと呼ばれる．
潜在変数のパラメータ$\pi_k$が各カテゴリの重みに対応する．(混合係数)

### 対数周辺尤度をとる

解析的に解けない．

### EMアルゴリズム

遷座変数に対して周辺化した，同時分布を用意．
logの中に，$\sum$があると，解析的に解けないのがわかってる．

#### Eステップ

#### Mステップ

### 混合モデルに対するEMアルゴリズム

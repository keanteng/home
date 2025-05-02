---
title: Pythonを使ったガチャ計画
description: 崩壊：スターレイルのワープ計画
date: 2024-04-30
draft: false
author: Kean Teng Blog
tags: ["ガチャ", "Python", "シミュレーション", "ゲーム"]
weight: 5
summary: ガチャゲームは、ルートボックスに似たガチャメカニズムを導入したビデオゲームで、プレイヤーがゲーム内通貨を使ってランダムなゲーム内アイテムを入手するよう誘惑します。
---

<center><img src=https://images.unsplash.com/photo-1518895312237-a9e23508077d?q=80&w=1784&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" class="center"/></center>
<p style="text-align: center; color:grey;"><i>Unsplashからの画像</i></p>

> *この記事で提供される情報は教育目的のみであり、ガチャゲームプロバイダーとは一切関連がありません。*

ガチャゲームは、ルートボックスに似たガチャメカニズムを導入したビデオゲームで、プレイヤーがゲーム内通貨を使ってランダムなゲーム内アイテムを入手するよう誘惑します。このようなメカニズムは2010年代初頭、特に日本で広く使われるようになり、時間とともにモバイルゲーム文化の不可欠な一部となりました。人気のあるガチャゲームには、原神、Arknights、エピックセブン、崩壊：スターレイルなどがあります。これらのゲームは、優れたアート、音楽、ストーリー、そしてロト形式のシステムによるゲーム内アイテム収集を特徴としており、世界中でますます人気が高まっています。

<center><img src=https://prod.assets.earlygamecdn.com/images/HonkaStarRail-Banner.jpg?mtime=1677074676 class="center"/></center>
<p style="text-align: center; color:grey;"><i>崩壊：スターレイル</i></p>

## はじめに

ガチャゲームは基本的に世界中のユーザーが無料でプレイできます。しかし、ガチャメカニズムにより、ゲーム内でレアまたは強力なアイテムを入手することは、非常に低い確率で難しいことが多いです。さらに、限られたゲーム内通貨は、プレイヤーがガチャを通じてランダムなゲーム内アイテムと交換する際に急速に枯渇します。ゲームの高額さは、ゲーム内通貨の価格設定に起因しており、プレイヤーはガチャシステムの低い確率性のために、実際のお金を多額費やしてジャックポットを狙う機会を得る必要があります。

ガチャゲームは基本的に無料でプレイできるものの、収益性の高いビジネスです。このメカニズムは、ゲーム内で標準バナーと期間限定バナーとして実装されることが一般的で、標準バナーではプレイヤーが「引く」ことができる永続的に利用可能なアイテムが特徴です。一方、期間限定バナーは、特定の期間内にのみ入手可能な賞品のみを含みます。

<center><img src=https://dotgg.gg/wp-content/uploads/sites/16/2024/02/image.jpg" class="center"/></center>
<p style="text-align: center; color:grey;"><i>ガチャゲームの収益</i></p>

崩壊：スターレイルを例に挙げると、このゲームは標準および期間限定キャラクターバナーを備えており、プレイヤーは各バナーから**0.6%**の確率で5つ星キャラクターを、**5.1%**の確率で4つ星キャラクターまたはライトコーンを獲得できます。良い点は、プレイヤーがバナーで合計**90回引く**と、5つ星キャラクターが保証されることです。しかし、期間限定キャラクターバナーでは少し異なり、プロモーションの5つ星キャラクターが50%の確率で出現します。もし受け取った5つ星キャラクターがそのキャラクターでなかった場合、次に5つ星を引く際にそのキャラクターが保証されます。

## 5つ星を入手する方法は？
バナーで与えられた引く確率以下のランダムな数値が得られれば、5つ星を獲得できます。ランダムな数値は、サーバー上で0から1の間で均等に生成されます。以下は引く確率の計算方法です：

```py
def get_rate(warp):
    # 74回目の引くまで基本確率は同じ
    if warp < 74:
        return 0.06
    
    # 73回目の引く以降、90回目まで毎回6%ずつ確率が上昇
    elif warp < 90 and warp >= 74:
        return (warp - 73) * 0.06 + 0.006
    
    else:
        return 1.00
```

引くの分布を見つけるのは少し複雑で、幾何分布や再帰的な確率計算が関わります。基本的には、74回未満の引くについては、幾何分布を使用して確率を計算できます：

```
prob(X = k) = p * (1 - p)^(k-1)

p : 成功の確率
k : 試行回数
```

73回目の引く以降、90回目（保証）まで引くごとに確率が変化します：

```py
P(X < 74) = P(X < 73) + (1 - P(X < 73)) * get_rate(74)
P(X < 75) = (1 - P(X < 73)) * get_rate_mult(75) * get_rate(75) + P(X < 74)
P(X < 76) = (1 - P(X < 73)) * get_rate_mult(76) * get_rate(76) + P(X < 75) + P(X < 74)

# 関数
def get_rate_mult(warp):
    if warp == 75:
        return 1 - get_rate(74)
    
    else:
        return (1 - get_rate(warp - 1)) * get_rate_mult(warp - 1)
```

引くの分布を見ると、いくつかの興味深い洞察が得られます：

<center><img src="https://github.com/keanteng/honkaistarrail/blob/main/image/image1.png?raw=true"  class = "center"/></center>

最初のプロットは、5つ星の引くの分布を示しており、約70〜80回の引くで5つ星を獲得する可能性が高いことがわかります。74回目の引くあたりで、累積確率曲線が急上昇し、確率の増加を示しています。3番目のプロットも同様です。プロットの作成については、[こちら](https://github.com/keanteng/honkaistarrail/blob/main/2_hsr_gacha_system.ipynb)を参照してください。実際、約80回の引くを準備しておけば、少なくとも1つの5つ星を期待できると安全に想定できます。

## 期待値の設定
これまでの計算では、ピティやキャラクター保証のケースは考慮していませんでした。実際のシナリオでは、プレイヤーは自分が欲しい5つ星のコピーを入手する確率を知りたいとき、さまざまな条件を持っています。Pythonを使用して何度もシミュレーションを行い、平均を取ることで推定確率を得ることができます。シミュレーションでは、引く回数、アカウントのキャラクター保証の状態、アカウントのピティ量を考慮する必要があります。

```py
def calculate_char_probability(
    warps,
    character_pity,
    character_guaranteed,
    character_copies,
    num_simulations
):
    ...
    ...
    estimated_probability = successful_simulations / num_simulations

    return estimated_probability
```

ゲームアカウントの進行状況に基づいて関数にいくつかの入力値を与えると、目標達成の可能性を教えてくれます。もちろん、これによりより効果的に引くことができ、期待値を高め、失望を減らすことができます：

```
calculate_char_probability(
    warps,
    character_pity,
    character_guaranteed,
    character_copies,
    num_simulations
)

# 戻り値
推定確率: 0.90
```

完全なコードは[こちら](https://github.com/keanteng/honkaistarrail/tree/main)を参照してください。

## 参考文献
参考になる優れたリソースや動画：
- https://github.com/Jose-AE/hsr-warp-calculator
- https://github.com/sr229/gacha-prng
- https://www.youtube.com/watch?v=gZGW190E3ok
```
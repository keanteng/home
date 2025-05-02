---
title: "EPFで100万円への道"
description: "退職目標の予測"
date: "2024-01-09"
draft: false
author: "Kean Teng Blog"
tags: ["Python", "Streamlit", "EPF", "KWSP", "Finance"]
weight: 5
summary: "EPFまたはKWSP（従業員積立基金）は、労働者が退職貯蓄を保護するのを支援するために1951年にマレーシア政府によって設立されました。"
math: true
---


<center><img src="https://images.unsplash.com/photo-1590283603385-17ffb3a7f29f?q=80&w=1770&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Unsplash から画像</i></p>

EPFまたはKWSP（従業員積立基金）は、労働者が退職貯蓄を保護するのを支援するために1951年にマレーシア政府によって設立されました。生活費の高騰と経済情勢の変化により、早期退職を考えている人々にとって課題となっています。「早期に退職するためにどれくらいの貯蓄があれば十分か？」は、快適な退職を求める人々にとって切実な問題となっています。

HSBCのQuality of Lifeレポートによると、マレーシアのミレニアル世代は、快適に退職するためには平均486万リンギットが必要だと述べています。さらに、同じ目的のために、ジェネレーションXは平均453万リンギット、ベビーブーマーは平均257万リンギットが必要になるとしています。

個人的には、これは懸念すべき金額です。医療、高税率、住居などの分野での支出の増加は、人々が潜在的に長期にわたる退職期間を維持するのに十分な貯蓄を蓄積することを困難にしています。もちろん、個人の退職貯蓄の適切な計画と予測が、退職段階での目標とライフスタイルの期待を達成するための鍵であることも不可欠です。

それだけでなく、EPFによると、会員のわずか4％が50万1リンギットから100万リンギットの貯蓄を持っており、54歳になった会員の35％以上が1万リンギット未満の貯蓄しか持っていません。したがって、口座から貯蓄を引き出すような行動は、非常に悪い考えである可能性があります。

この調査では、退職貯蓄目標を100万リンギットに設定し、個人の月給と年間のボーナスに応じて目標達成にかかる年数を決定することに関心があります。

<iframe
  src="https://jf7sray2rbu8bgclqnvaxu.streamlit.app/?embed=true"
  height="850"
  width="700"
  style="border:none;"
></iframe>

驚くべきことに、月給3000リンギットを稼いでいる場合、貯蓄が100万リンギットに達するのにかかるのはわずか31.29年です。

```
total_1_year = 12 * 728 * (1 + i)
total_2_year = 12 * 728 * (1 + i) + 12 * 728 * (1 + i)^2
...
...
```

年数を検索するために、Pythonを使用して目標シーク関数を開発し、関心のある任意の金額を検索できます。

$$
10^6 = 12 \times x \times (r + r^2 + r^3 + ... + r^n) 
$$
$$\iff n = \frac{1}{ln(r)}\times ln(\frac{10^6 \times (r-1)}{12xr} + 1)$$

この方程式をPythonコードに変換して実行できます。

```py
def goal_seek(goal, contribution, bonus_contribution, compound_rate):
    r = 1 + compound_rate/100
    contribution = contribution + bonus_contribution/12
    years = 1/np.log(r)*np.log((goal*(r-1))/(12*contribution*r) + 1)
    return years
```

年間のボーナスがこの方程式にどのように適合するのか疑問に思われるかもしれません。この方程式では、利息が年ごとに支払われると仮定されており、したがって年間のボーナスは月々の支払いに分割できます。

```
contribution = bonus/12 + monthly_salary
```

ただし、年利が毎月与えられる場合、このような仮定は貯蓄の不正確な予測を引き起こす可能性があることに注意してください。

## 注意事項

マレーシア人と非マレーシア人では、EPFの拠出率が異なります。もちろん、給与額によっても拠出率が若干異なります。例えば、給与が5000リンギット以下の場合、雇用主の拠出率は13％であり、それ以上では12％になります。拠出額は常にセントなしで最も近いリンギットに切り上げられる必要があります。さらに、月々の拠出率は[第三スケジュール](https://www.kwsp.gov.my/documents/20126/140690/Jadual+Ketiga+BI.pdf)を参照できます。
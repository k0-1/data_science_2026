# 第9回 データの可視化：クロス集計

## 本講義の目標
クロス集計の方法とその可視化手法について学ぶ．

--- 

## クロス集計とは？

2つ（以上）の変数の関係を見るためのデータ分析手法の一つ．
2つの項目を縦軸と横軸に設定し，
それぞれのカテゴリーが交差する部分の度数や割合をまとめたクロス集計表を作る．
これにより，カテゴリごとの傾向などがわかる．
顧客満足度調査やマーケティングなどで活用されている．

今回も将棋AIのデータ（`floodgate_data2026.csv`）と
Pandasを用いてクロス集計の流れを習得しよう．

--- 
## 1. 準備：データの読み込み

まずは，前回の演習で使用したデータ（`floodgate_data2026.csv`）と，データ分析の基本ツールであるPandasを読み込む．
（※事前に左側のフォルダアイコンから，CSVファイルをアップロードしておくこと．
`floodgate_data2026.csv`は[https://github.com/k0-1/data_science_2026/tree/main/sessions/07_pandas](https://github.com/k0-1/data_science_2026/tree/main/sessions/07_pandas)からダウンロードできる．）

```python
import pandas as pd

# データの読み込み
df = pd.read_csv('floodgate_data2026.csv')
display(df.head())
```

グラフの装飾に用いるライブラリ`matplotlib`と
日本語化ツール`japnize_matplotlib`
もあらかじめ読み込んでおこう．

```python
import matplotlib.pyplot as plt
```

```python
!pip install japanize-matplotlib
import japanize_matplotlib
```

---

## 2. クロス集計

振り飛車であるか否かがどう勝率に影響しているか見てみよう．

まずは先手について調べてみる．
クロス集計を行うには，
`pd.crosstab(比較したい属性1, 比較したい属性2)`
というコマンドを使えばよい．


```python
cross_tab_sente = pd.crosstab(df['先手振り飛車'],df['勝者'])
display(cross_tab_sente)
```

行ごとあるいは列ごとに割合を知りたいときは，それぞれ，
`normalize='index'`, `normalize='columns'`というオプションをつけるとよい．

```python
cross_tab_sente_rate= pd.crosstab(df['先手振り飛車'],df['勝者'], normalize='index')
display(cross_tab_sente_rate)
```

実行して得られるクロス表から，先手について，振り飛車を採用したときの勝率がそうでないときに比べて低くなっていることがわかる．

### 練習問題
後手振り飛車についても度数の場合と，行ごと割合の場合のクロス集計表を同様に作ろう．

<details>
<summary>解答例を見る（クリックで展開）</summary>

>```python
>cross_tab_gote = pd.crosstab(df['後手振り飛車'],df['勝者'])
>display(cross_tab_gote)
>```

>```python
>cross_tab_gote_rate = pd.crosstab(df['先手振り飛車'],df['勝者'], normalize='index')
>display(cross_tab_gote_rate)
>```
</details>

---

## 3. 帯グラフによる可視化
クロス集計表の傾向を一目でわかるようにする方法の一つとして，帯グラフ（積み上げ横棒グラフ）がある．
棒グラフ(bar)を横(horizontal)に積み上げる(`stacked=True`)という意味で，
`plot.barh(stacked=True)`を用いるとよい．

度数の場合は次のように書ける：
```python
cross_tab_sente.plot.barh(stacked=True)
```

割合を比較したい場合は割合をクロス表にした`cross_tab_sente_rate`のほうを使えばよい．
```python
cross_tab_sente_rate.plot.barh(stacked=True)
```

**注意**
- 縦の棒グラフにしたいときは`plot.bar(stacked=True)`とすればよい．
- 列ごとに棒を作りたいときは，`plot.barh`を行う前に`.T`によって表を転置する必要がある．
- 色味を変えたいときは，オプションとして`colormap='Pastel1'`などのようにcolormapを変えるとよい．

### 練習問題
後手についても同様に帯グラフによる可視化をしてみよう．

<details>
<summary>解答例を見る（クリックで展開）</summary>

>```python
>cross_tab_gote.plot.barh(stacked=True)
>```

>```python
>cross_tab_gote_rate.plot.barh(stacked=True)
>```
</details>


## 提出課題
ここまでは，全体の傾向として先手振り飛車か否かと勝ち負けの関係を見てきた．課題では，振り飛車をよく採用する将棋AI`taiyo_unajyu-f`について先手のときの振り飛車と勝敗の関係を可視化してみよう．

**手順**

0. `target='taiyo_unajyu-f'`とおく．
1. 先手が`target`であるものを集めたデータフレームを`df_target`とする．
2. `df_target`に対し，df['先手振り飛車'], df['勝者']の関係を割合のクロス表にし，`df_target_crosstab`とする
3. `df_target_crosstab`を帯グラフにして，画像データ(png)として出力してください．
4. 提出前に確認の上，画像データを学生証番号.pngとして提出してください．


グラフのGoogle Colabの保存方法や画像のダウンロード方法については，第8回[https://github.com/k0-1/data_science_2026/blob/main/sessions/08_pandas/lecture_08.md](https://github.com/k0-1/data_science_2026/blob/main/sessions/08_pandas/lecture_08.md)を参照．


**余裕のある人向け**：`target`に別の将棋AI名に代入だけで，同じコードでその将棋AIについての可視化が可能である．気になる将棋AI名があれば調べてみよう．


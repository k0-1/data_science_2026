# 第12回 データサイエンス演習 

## 本講義の目標
今回はこれまで学んできたことを実際のデータに適用し，
データ分析手法の定着を図る．

---

まずはpandasを使えるようにする．
```python
import pandas as pd
```

matplotlibも読み込む．
```python
import matplotlib.pyplot as plt
```

日本語化ツールをインストールし，読み込む．
```python
!pip install japanize-matplotlib
import japanize_matplotlib
```

# 1. 日本の大学のデータ

データは以下のKaggleのサイトから取得する．
[https://www.kaggle.com/datasets/webdevbadger/japanese-universities](https://www.kaggle.com/datasets/webdevbadger/japanese-universities)

ただし，Kaggleの利用にはアカウント登録が必要です．

今回は，Kaggleからダウンロードしたデータをおいた講義資料置き場から読み込む．

```python
df_univ=pd.read_csv('https://raw.githubusercontent.com/k0-1/data_science_2026/main/sessions/12_pandas/japanese_universities.csv')
display(df_univ)
```

各columnの説明は以下の通りです．
- code: the identification codes the Ministry of Education assigned.
- name: the official English translation of university names.
- name_jp: the official Japanese university names.
- type: the type of university; one of National, Public, or Private.
- type_jp: the type of university in Japanese.
- address: the address of universities.
- postal_code: the postal codes.
- phone: the phone numbers.
- state_code: the codes associated with the state.
- state_jp: the state of universities.
- latitude: the latitude of university locations.
- longitude: the longitude of university locations.
- found: the year and month the university was founded.
- faculty_count: the number of faculties in universities.
- department_count: the number of departments in universities.
- has_grad: the availability of the graduate school. True or False.
- has_remote: the availability of the remote school. True or False.
- review_rating: the review rating of universities.
- review_count: the number of reviews of universities.
- difficulty_SD: the standard deviation measurement of universities' difficulty.
- difficulty_rank: the rank of universities' difficulty. The highest to lowest ranking order is - [S,A,B,C,D,E,F]

## 課題1-1 (50点)
以下を提出してください．(50点取るには２つとも提出する必要があります．）
- `faculty_count`（学部数（大学院含む））の多い大学トップ10について，その学部数をわかりやすく可視化したグラフ画像（1-1.pngという名前にしてください）
- そのグラフ画像から読み取れること(テキスト)

## 課題1-2 (50点)
以下を提出してください．(50点取るには２つとも提出する必要があります．）
- `difficulty_SD`と`review_rating`の関係を散布図によって可視化したグラフ画像(1-2.pngという名前にしてください)
- そのグラフ画像から読み取れること（テキスト）

---

# 2. スターバックスの栄養素データ（2021年末）

以下のサイトからデータを読み込む．
[https://github.com/rfordatascience/tidytuesday/blob/master/data/2021/2021-12-21/readme.md](https://github.com/rfordatascience/tidytuesday/blob/master/data/2021/2021-12-21/readme.md)

```python
df_starbucks =pd.read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-12-21/starbucks.csv')
display(df_starbucks.head(10))
```


## 課題2-1 (50点)
以下の2つを提出してください．
- `size`がtallであるものを集めたDataFrameを`df_tall`とおく．
`df_tall`について`calories`, `total_fat_g`, `sugar_g`を軸として，
その三つの値を用いてK-means法を適用し，4つにグループ分けをし，
それをplotlyの3D散布図を用いて可視化してください．
得られたHTMLを`2-1.html`という名前で提出してください．
- `2-1.html`を見て読み取れることをテキストで提出してください．




---


# 3. 主要ファストフード8社 総合比較データ
以下のサイトからファストフードのデータを読み込む．
[https://github.com/rfordatascience/tidytuesday/blob/master/data/2018/2018-09-04/readme.md](https://github.com/rfordatascience/tidytuesday/blob/master/data/2018/2018-09-04/readme.md)


```python
df_fastfood = pd.read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-09-04/fastfood_calories.csv')
display(df_fastfood.head(10))
```

## 課題3-1 (50点)
以下の２つを提出してください．(50点取るには２つとも提出する必要があります．）
- `restaurant`ごとにメニューの`calories`の平均値および`sodium`(塩分量)の平均値を求め，棒グラフとして可視化してください．
得られた棒グラフは`3-1.png`という名前で提出してください．
- 得られたグラフをみてわかることを書いてください．

---

# 4. ポケモン種族値データ
以下のサイトからポケモンの種族値データを読み込む．
```python
df_poke = pd.read_csv('https://gist.githubusercontent.com/armgilles/194bcff35001e7eb53a2a8b441e8b2c6/raw/92200bc0a673d5ce2110aaad4544ed6c4010f687/pokemon.csv')
display(df_poke.head(10))
```

## 課題4-1 (50点) 
以下を提出してください．
(50点取るには２つとも提出する必要があります．）
- `Type 1`ごとの伝説ポケモンの割合をクロス集計によって求め，
その割合を帯グラフによって可視化してください．その帯グラフを`4-1.png`という名前で提出してください．
- さらに，その可視化によって気づいたことを書いてください．

## 課題4-2 (50点)
ポケモンのタイプについての強さランキングを作りたいです．
以下を提出してください．
(50点取るには２つとも提出する必要があります．）
- `Type 1`ごとに`total`の平均値を計算して得られる値を棒グラフにして可視化してください．その棒グラフを`4-2.png`という名前で提出してください．
- その可視化によって気づいたことを書いてください．




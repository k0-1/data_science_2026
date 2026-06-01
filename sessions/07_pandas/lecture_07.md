#  Pandas入門 2: 外部データの取り込みと集計・ファイル出力

##  今回の目標
データ分析における実務の基本フローである「データの取り込み」「集計・加工」「ファイル出力」の一連の流れを習得する．

今回は，実際の将棋AI対局データを用いて，「どの将棋AIが最も強いのか」を明らかにするランキングを作成する．

データは[Floodgate](https://wdoor.c.u-tokyo.ac.jp/shogi/)の2026年の実際の対局データを集計したものである．

## 0. CSVファイルとは何か？

これから読み込む **CSV（Comma-Separated Values）** とは，表形式のデータを保存するための最もシンプルで世界標準のファイル形式である．
中身は以下のように，データが「カンマ（`,`）」で区切られたただの文字の羅列になっている．

```text
対局ID,先手,後手,勝者
game001,miao4,dlshogi,先手
game002,Apery,miao4,後手
```

Python（Pandas）などのプログラムを使うと，この文字の羅列を自動的に整った「表（データフレーム）」として読み込むことができる．データ分析の世界では，特定のソフト（Excelなど）に依存せず動作が軽いCSVファイルが標準的に用いられる．


## 1. データの準備と取り込み

世の中に公開されているデータを，自身の分析環境（Google Colab）に取り込んで読み込む．

### ① データのアップロード
1. [https://github.com/k0-1/data_science_2026/tree/main/sessions/07_pandas](https://github.com/k0-1/data_science_2026/tree/main/sessions/07_pandas)にある `floodgate_data2026.csv` を自分のパソコンにダウンロードする．
2. Google Colabの画面左側にある「フォルダのアイコン」をクリックする．
3. 左側のメニューの空間に向かって，ダウンロードしたCSVファイルを**ドラッグ＆ドロップ**してアップロードする．

### ② Pandasでデータを読み込む
以下のセルを実行して，アップロードしたCSVファイルを読み込んでみよう．

```python
import pandas as pd

# ファイル名を指定して読み込み，DataFrameとして取り込む
df = pd.read_csv('floodgate_data2026.csv')

# 最初の20行を表示して，正しく取り込めたか確認する
display(df.head(20))
```

`len()`関数を使うことで行数がわかる：
```python
print(len(df))
```

---

## 2. データの観察と並べ替え

データ分析を始める前に，データがどのような状態になっているかを観察する．

### ① 表の表示設定を変更する
データの一部が「...」と省略されて見えない場合は，Pandasの設定を変更することで全貌を確認できる．

```python
# ファイル名などが「...」で省略されないように，表示幅の制限を解除する
pd.set_option('display.max_colwidth', None)

# 行の表示件数も100行に拡大してみる
pd.set_option('display.max_rows', 100)

display(df.head(100))
```

### ② データを並べ替える（ソート）
特定の列を基準に，データを並べ替えてみよう．

```python
# '手数' が長い順（降順：ascending=False）に並べ替える
df_long = df.sort_values('手数', ascending=False)
display(df_long.head())
```

---

## 3. 最強AIランキングの作成

このデータに登場する将棋AIの中で，最もレーティング（強さの指標）が高いAIのトップ10を抽出する．

### ① 先手と後手のデータを縦に連結する
「先手」として参加したデータと「後手」として参加したデータを1つにまとめるため，列の名前を揃えてから縦に連結させる．

注意：列名が異なるとうまく縦に連結されない．横に連結させたい場合はaxis=1をオプションとして書く必要がある．


```python
# 先手と後手の「名前」と「レーティング」をそれぞれ抽出し，列名を統一する
df_sente = df[['先手', '先手レーティング']].rename(columns={'先手': 'AI名', '先手レーティング': 'レーティング'})
df_gote = df[['後手', '後手レーティング']].rename(columns={'後手': 'AI名', '後手レーティング': 'レーティング'})

# 2つの表を縦に連結（concat）させる．
df_all_ratings = pd.concat([df_sente, df_gote])
display(df_all_ratings.head(100))
```


### ② グループ化して最高値を出す
`groupby` を使ってAIごとにデータをグループ化し，それぞれの最高レーティングを算出する．

`groupby`の基本構文：`df.groupby('基準にする列名')['計算したい列名'].集計関数()`

```python
# 'AI名'ごとにグループ化し，レーティングの最高値（max）を計算する
ai_max_ratings = df_all_ratings.groupby('AI名')['レーティング'].max()

# 高い順に並べ替えたトップ10のDataFrameを作る
top10_ai = ai_max_ratings.sort_values(ascending=False).head(10)

print("最強将棋AIランキング トップ10")
display(top10_ai)
```

注意：groupbyによって'レーティング'のみ抜き出してmaxを取っているので，
`ai_max_ratings`はSeries扱いされる．
そのため`sort_values`の引数は自明となるのでオプションのみ書く．

### 練習問題
振り飛車を指すAIのランキングを作りたい．

0. 先手が振り飛車を指したデータ（'先手振り飛車'がTrueになっているもの）だけを集めたDataFrame `df_sente_furi`を作ってください．
同様に後手が振り飛車を指したデータだけを集めたDataFrame `df_gote_furi`をつくってください．
1. `df_sente_furi`から先手の名前と先手レーティングを抽出し，列名を'AI名', 'レーティング'としたDataFrame `df_sente_furi_rating`をつくってください．
`df_gote_furi`から後手の名前と後手レーティングを抽出し，列名を'AI名'，'レーティング'としたDataFrame `df_gote_furi_rating`をつくってください．
2. `df_sente_furi_rating`と`df_gote_furi_rating`を連結したDataFrame`df_furi_rating`をつくってください．
3. `groupby`を使って'AI名'ごとにグループ化し，レーティングの最高値を計算し，高い順に並べたトップ10を表示してください．

<details>
<summary>解答例を見る（クリックで展開）</summary>

>0. 
>```python
>df_sente_furi = df[ df['先手振り飛車']==True]
>df_gote_furi = df[ df['後手振り飛車']==True]
>```
>1. 
>```python
>df_sente_furi_rating = df_sente_furi[ ['先手', '先手レーティング']].rename(columns={'先手': 'AI名', '先手レーティング': 'レーティング'})
>df_gote_furi_rating = df_gote_furi[ ['後手', '後手レーティング']].rename(columns={'後手': 'AI名', '後手レーティング': 'レーティング'})
>```
>2. 
>```python
>df_furi_rating = pd.concat([df_sente_furi_rating, df_gote_furi_rating])
>```
>3.
>```python
>df_furi_rating = pd.concat([df_sente_furi_rating, df_gote_furi_rating])
>display(df_furi_rating.groupby('AI名')['レーティング'].max().sort_values(ascending=False))
>```
</details>

---

## 4. データのファイル出力

分析して作成したランキングデータを，パソコンのExcelなどでいつでも見られるようにCSVファイルとして出力・保存する．
`to_csv('保存するファイル名', オプション)`とするとDataFrameをcsvに保存できる
（今の環境では，Google Colabに保存される）．


```python
# 分析結果（ランキング）をCSVファイルとして保存する
# index=False：不要な行番号を消す設定（必須！）
# encoding='utf-8-sig'：Excelで開いたときの日本語の文字化けを防ぐ設定（必須！）
top10_ai.to_csv('ai_ranking.csv', index=False, encoding='utf-8-sig')
```

`google.colab`というパッケージの`files`モジュールをimportしてColabから自分のパソコンにファイルをダウンロードする：
```python
from google.colab import files

# Colabから自分のパソコンにファイルをダウンロードする
files.download('ai_ranking.csv')
```

---

## 【提出課題】絶対王者「miao4」の戦績レポートを作成せよ

前半の演習で，このデータにおける絶対王者が「miao4」であることが判明した．
「miao4」の戦績だけを抽出して，csvファイルとして保存しよう．

### 【課題の指示】
以下の条件を満たすコードを下の空のセルに記述・実行し，出力されたCSVファイルを提出すること．

1. **データの抽出**
   元のデータフレーム（`df`）から，**`miao4` が「先手」または「後手」として対局しているデータのみ**を抽出すること．（ヒント：前回学んだ`|` 記号を用いたOR条件のフィルタリングを使用する）
2. **データの確認**
   抽出したデータが全部で何局分あるか，`len()` 関数を用いて画面に表示すること．
3. **成果物の出力**
   抽出した「miao4の全対局データ」を，**`miao4_games.csv`** というファイル名で出力すること．出力の際は，講義で学んだ「行番号を消す設定」と「Excelでの文字化けを防ぐ設定」を必ず適用すること．
4. **提出**
   自身のパソコンにダウンロードした `miao4_games.csv` を，本課題の提出フォームにアップロードすること．


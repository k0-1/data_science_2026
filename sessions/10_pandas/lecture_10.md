# 第10回 データの可視化：クロス集計の応用

## 本講義の目標
クロス集計を応用し，複数カテゴリについて同時に可視化する方法を学ぶ．

前回，振り飛車か否かと勝率の関係，あるいは特定の一つのAIについての振り飛車か否かと勝率の関係を可視化した．

実務では，特定の一つのものだけでなく，同時に複数のもののある性質を比較したいという場面が多くありうる．
その際にもクロス集計が有効であることを見る．

その例として，今回は先手番と後手番でどう勝率が異なるかについて複数の将棋AIを比較してみたい．


## 1. 準備：データの読み込み
まずは，前回使用したデータ（floodgate_data2026.csv）と，データ分析の基本ツールであるPandasを読み込む．
今回はURLから直接csvファイルをDataFrameとして読み込んでみよう．

```python
import pandas as pd

# データの読み込み
df = pd.read_csv('https://raw.githubusercontent.com/k0-1/data_science_2026/main/sessions/07_pandas/floodgate_data2026.csv')
display(df.head())
```

ライブラリ`matplotlib`を読み込む．
```python
import matplotlib.pyplot as plt
```

日本語化ツールをインストールし，読み込む．
```python
!pip install japanize-matplotlib
import japanize_matplotlib
```

## 2. クロス集計の復習
クロス集計を行うには，
`pd.crosstab(比較したい属性1, 比較したい属性2, normalize='index')`
というコマンドを使えばよい．


たとえば，先手番について，振り飛車であるか否かがどう勝率に影響しているか次のようにみることができるのだった．

```python
cross_tab_sente_rate= pd.crosstab(df['先手振り飛車'],df['勝者'], normalize='index')
display(cross_tab_sente_rate)
```

これを帯グラフとして可視化するには`plot.barh(stacked=True)`とすればよかった：

```python
cross_tab_sente_rate.plot.barh(stacked=True)
```

## 3. クロス集計表から必要な情報を取り出す．

以下の二つの将棋AIについて，先手番のときの勝率を調べてみよう．

```python
target_ais = ['taiyo_unajyu', 'taiyo_unajyu-f']
```

これらの将棋AIが先手番の対局をすべて集めたDataFrameを作りたい．
そのようなときには，`isin(リスト)`を用いるとよい．
これは，リストの中にあるときはTrueを返し，ないときにはFalseを返すメソッドである．

```python
df_target_sente = df[df['先手'].isin(target_ais)]
display(df_target_sente.head())
```

次に
`df_target_sente`の先手について勝敗割合をクロス集計表にしてみよう．

```python
target_cross_sente = pd.crosstab(df_target_sente['先手'], df_target_sente['勝者'], normalize='index')
display(target_cross_sente)
```

最後に先手の勝率を取り出す．
そのためには`勝者`が`先手`になっている列を取り出せばよい．


```python
sente_win_rate=target_cross_sente['先手']
display(sente_win_rate)
```


### 練習問題
'taiyo_unajyu', 'taiyo_unajyu-f'について後手番のときの勝率を調べてみよう．

<details>
<summary>解答例を見る（クリックで展開）</summary>

>```python
>df_target_gote = df[df['後手'].isin(target_ais)]
>display(df_target_gote.head())
>target_cross_gote = pd.crosstab(df_target_gote['後手'], df_target_gote['勝者'],normalize='index')
>display(target_cross_gote)
>gote_win_rate=target_cross_gote['後手']
>display(gote_win_rate)
>```

</details>

## 4. 得られた2つのDataFrameを1つのDataFrameに合体する

`sente_win_rate`と`gote_win_rate`のインデックス（名前）がそろっているので，次のように辞書型を使って`{'キー': 値}`とすると列の名前をキーとするようなDataFrameをよしなに作成してくれます．

```python
df_target_win_rate = pd.DataFrame({
    '先手番勝率': sente_win_rate, 
    '後手番勝率': gote_win_rate
})
display(df_target_win_rate)
```

## 5. グラフの描画
`plot.barh`でオプション`stacked = True`をつけない場合は，
棒が並んで表示される．
このとき列の左（先手番勝率）から順に，下から上に棒が表示される


```python
df_target_win_rate.plot.barh()
plt.title('手番別 勝率比較', fontsize=16)
plt.xlabel('勝率 (0.0 〜 1.0)', fontsize=12)
plt.ylabel('AI名', fontsize=12)
plt.xlim(0, 1) # X軸の最大値を1.0（100%）に固定する
plt.legend(bbox_to_anchor=(1.05, 1), loc='upper left')
plt.grid(axis='x', linestyle='--', alpha=0.7) # 見やすくするために縦に補助線を入れる
plt.show()
```

## 提出課題

対局数ランキングTop20の将棋AIについて
上記と同様に先手勝率と後手勝率を棒グラフにして，その画像を提出してください．
また，そのグラフを見て気づいたことを書いて提出してください．

ヒント：第10回課題（6/18分）の解答例が参考になります．
[https://tlms.tsc.u-tokai.ac.jp/pluginfile.php/2749450/mod_resource/content/1/kadai_0618-ans.pdf](https://tlms.tsc.u-tokai.ac.jp/pluginfile.php/2749450/mod_resource/content/1/kadai_0618-ans.pdf)

解答例内の`top20_ai_wrt_number_of_games`が
対局数ランキングTop20の将棋AIをindexとした対局数を表すSeriesになっています．
indexをリストにするには，`index.tolist()`というメソッドを用います．

```python
target_ais_20 =top20_ai_wrt_number_of_games.index.tolist() 
```



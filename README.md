# Rossmann-Store-Sales
![image](https://user-images.githubusercontent.com/69502527/156883458-9fc3ca8e-b657-4621-926f-3d1470475e0b.png)

## このコンペの目標
* 上位10%以内のスコアを出す。(できれば5%以内)
* EDA, バリデーション, アンサンブルなどの手法をぶつける
* kaggle本を辿って本の内容、コンペの進め方を理解する


## コンペの概要
### データ
時系列. trainは2015-07-31までのデータが含まれていて、testは2015-08-01からのデータとなっている. <br>
バリデーションとかに注意しないとリークが起こる可能性が高い.<br>
testにはCustomersとSalesがない
### 評価指標
RMSPE- kaggle本に載ってない. sklearnのmoduleにないのかな？⇨ない(絶望)<br>
自分で関数定義して使うしかないらしい(先人の知恵を借りよう)

## 仮説
### データを見て率直に感じたこと<br>
Trainのカラムは基本的に重要. storeはAssortment, CompetitionDistance, Promoが重要っぽそう.<br>

## 方針
[公開ノートブック(score:0.3)](https://www.kaggle.com/ashwathbalaji/rossmann-store-sales)を基準にkaggle本を読みながら改良<br>
もうきついなあと思ったら上位のノートブックを参照して参考にする.

## Data
### Files
* train.csv - historical data including sales
* test.csv - historical data including sales
* sample_submission.csv - a sample submission file in the correct format
* store.csv - suplemental information about the stores
### Data fields
* Id- テストデータのみにあるカラム. インデックス的な？一応説明にはStoreとDateのdupleって書いてある
* Store- storeごとについてるuniqueなid
* DayOfWeek- kaggleの説明にはないけどあるカラム. 1~7までを取る.
* Sales- 儲けた金額(これが予測するカラム)
* Customers- １日で来た客数
* Open- 店舗が営業してたかどうか. 0 = closed, 1 = open
* StateHoliday- 州の休日を表す. だいたい休日の時Open=0. a = public holiday, b = Easter holiday, c = Christmas, 0 = None
* SchoolHoliday- 公立学校の閉鎖の影響を受けたかどうかを表す.

ここまでがtrain.csv<br>



----------------------------------------------------------------------------------------
ここからstore.csv


* StoreType- 4種類のストアタイプがあるらしい. a, b, c, d
* Assortment- 品揃えのレベルを表すらしい. a = basic, b = extra, c = extended
* CompetitionDistance- 最寄りの競合店までの距離(メートル)
* CompetitionOpenSince[Month/Year]- 最も近い競合他社がオープンしたときのおおよその年月を表示
* Promo- ストアがpromo(宣伝)を実施しているかどうか
* Promo2- いくつかの店舗で継続的かつ連続的に行われているプロモーションに参加しているかどうか. 0 = store is not participating, 1 = store is participating
* Promo2- Promo2に参加を開始した年と暦週を記録
* PromoInterval- Promo2が開始される連続した期間を記述し、プロモーションがあらたに開始される月を指定.<br>
"Feb,May,Aug,Nov" は、2月、5月、8月、11月にプロモーションが開始されることを意味する.


### 生データを見て感じたこと、思ったことを書く

日曜日に休業してる(毎週やってない)storeがあるのでどう処理するか.<br>
時系列になってるので時系列の手法もいくつか試せそう.<br>
テストデータでOpen=0になってるのはSalesも0では？(trainにはOpen=0でSalesとCustomersが0以外のインデックスはない)<br>
storeはtrainとStoreを共通キーにして結合するべきって感じ.<br>
storeはいくつか空欄がある⇨読み込み時にnaにしとくべき<br>
PromoIntervalはちょっと処理しづらそう→とりあえず解除して良さそうで、notebookの扱い方とかを参考にした方がいい.

## kaggle日記
**20220307**<br>
生データを確認してkaggl本でリークとか評価指標とか読んだ.<br>
0から単純なモデルを組もうとしたけど失敗. 大人しくnotebookを読むと決意.

**20220308**<br>
[公開notebook](https://www.kaggle.com/omarelgabry/a-journey-through-rossmann-stores)をBaselineにすることを
決定

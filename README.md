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

## EDA
![image](https://user-images.githubusercontent.com/69502527/157567785-65ddcc98-085b-4a88-b072-eb22a862bf1a.png)
![image](https://user-images.githubusercontent.com/69502527/157567846-97174d22-9dfe-47c8-a828-fd7482abadcc.png)
![image](https://user-images.githubusercontent.com/69502527/157567885-d5797c88-cb01-4a81-931c-f5d1f9bee90b.png)
![image](https://user-images.githubusercontent.com/69502527/157567948-9c481082-ac9c-41aa-9078-571152b1df2b.png)
![image](https://user-images.githubusercontent.com/69502527/157568019-069e30ff-41b0-4820-880e-3a68dad4f942.png)
![image](https://user-images.githubusercontent.com/69502527/157568032-f9e41d81-e448-4b83-9604-9d3bc7d1709e.png)
![image](https://user-images.githubusercontent.com/69502527/157568051-0706bec8-ba72-4516-bd7f-963c810da4ec.png)
![image](https://user-images.githubusercontent.com/69502527/157568058-6950b657-2a28-4f0c-9dca-e6e85e5327e3.png)
![image](https://user-images.githubusercontent.com/69502527/157568104-457f1902-bcf3-4776-a428-6cfdec77505c.png)
![image](https://user-images.githubusercontent.com/69502527/157568147-68d2def4-5d46-4504-b812-b10fb139cec5.png)
![image](https://user-images.githubusercontent.com/69502527/157568165-1613198b-d144-44cc-aee7-fec9776a377c.png)
![image](https://user-images.githubusercontent.com/69502527/157568173-fbfee132-812b-41d2-896f-41e1062eca29.png)
![image](https://user-images.githubusercontent.com/69502527/157568205-f82d0c4e-11d0-4330-a446-b1a733b67fe0.png)
![image](https://user-images.githubusercontent.com/69502527/157568212-81e99884-7a7f-4a4f-b536-1b311dc82609.png)
![image](https://user-images.githubusercontent.com/69502527/157568223-aa97b17f-47c8-4a40-9899-04925740be13.png)


## kaggle日記
**20220307**<br>
生データを確認してkaggl本でリークとか評価指標とか読んだ.<br>
0から単純なモデルを組もうとしたけど失敗. 大人しくnotebookを読むと決意.

**20220308**<br>
[公開notebook](https://www.kaggle.com/omarelgabry/a-journey-through-rossmann-stores)をBaselineにすることにし
熟読⇨モデル作成前の処理方法は参考になるが、モデルに不満

**20220309**<br>
xgboostのモデルがしっかりしているnotebookを見つけてそれをbaselineにしたい<br>
[これ](https://www.kaggle.com/manishleo10/gradient-boosting-machines-gbms-with-xgboost#Problem-Statement)に決定
Private 0.38045<br>
Public 0.37078<br>
めっちゃ低いやん...取りあえずkaggle本見ながら改良して上がらなかったら点数高いnotebookみるわ

**20220310**<br>
kaggle本を読みながら特徴量エンジニアリング

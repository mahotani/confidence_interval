# 信頼区間

信頼区間（しんらいくかん、英: Confidence interval, CI）とは、母集団の真の値が含まれることが、かなり確信(confident)できる数値範囲。　例えば95％CIとは、この範囲に母集団の値が存在すると、95％確信できることを意味する。  
(Wikipediaより)

例の如く、言葉だけではいまいちピンときません。  ｼｮﾎﾞ━(´·ω·`)━ﾝ  
95%信頼区間を図に表してみるとこんな感じ・・・

<img width="955" alt="distillation" src="https://user-images.githubusercontent.com/39772824/93886349-42387b00-fd20-11ea-9599-73ead3982e01.png">

求めたいことは何となく分かるが実際の求め方がしっくり来ないと言う人多いと思います。  
なので今回は、ポケモンの身長と体重を例に取って考えてみたいと思います。

## 求める前に・・・

早速信頼区間を求めていきたいとこなのですが、データがないとどうにもなりません。  
なので、データ収集のためにポケモンを乱獲したいと思います。  

### 用意するもの

- Nitendo Switch(もしくはNintendo Switch Light)
- ポケットモンスター Let's Go! ピカチュウ(もしくはイーブイ)
- 気合

ポケモンGOも捕まえたポケモンの身長と体重が分かるのですが、家から出ず短時間でできるのがピカブイだったのでLet's Go!ピカチュウを使いました。  

### データ収集

以下のポケモンについて100匹ずつ捕まえて、身長と体重をそれぞれのcsvファイルにまとめました。  
身長と体重のcsvファイルについては[こちら](https://github.com/mahotani/confidence_interval/tree/master/csv_files)。  

**捕まえたポケモンたち**

- ガーディ
- キャタピー
- サンド
- ビリリダマ
- ピッピ
- ポッポ

## データの内訳を見てみる

信頼区間を求める前に、今回使用するデータの内訳をざっくり見てみます。  
Pythonで以下の2つのコードを書き、main.pyを実行すると様々な統計量が得られます。

- functions.py

```python:functions.py
# coding: UTF-8

import csv
import numpy as np
import pandas as pd

'''
    csvファイルの読み込み
'''
def read_csv(filename):
    with open(filename, 'r') as csv_file:
        data = list(csv.reader(csv_file))
    
    return data

'''
    csvファイルへの書き込み
'''
def write_csv(filename, data):
    with open(filename, 'w') as csv_file:
        writer = csv.writer(csv_file)
        for row in data:
            writer.writerow(row)

'''
    list内の要素をstringからfloatに変換
'''
def translate_to_float(string_list):
    float_list = []
    for element in string_list:
        float_list.append(float(element))
    return float_list

'''
    データの整形
    1. １行目の消去
    2. numpy配列への変換
    3. 転置
    4. string型からfloat型への変換+身長と体重を分ける
'''
def get_height_weight(data):
    # 1行目の消去
    del data[0]
    # numpy配列への変換
    data = np.array(data)
    # 転置
    data = data.T
    # string型からfloat型への変換+身長と体重を分ける
    heights = translate_to_float(data[0])
    weights = translate_to_float(data[1])

    return heights, weights

'''
    pandasを用いて身長と体重の様々な統計量を返す。
'''
def get_details(heights, weights):
    # 身長と体重のリストをpandasのデータフレームに変換
    height_data = pd.DataFrame(heights)
    weight_data = pd.DataFrame(weights)
    # 身長と体重の統計量を取得
    height_detail = height_data.describe()
    weight_detail = weight_data.describe()

    return height_detail, weight_detail

```

- main.py

```python:functions.py
# coding: UTF-8

import csv
import numpy as np
import functions

# 読み込みたいポケモンの名前を入力
# 使用できるポケモンの名前 -> {ガーディ, キャタピー, サンド, ビリリダマ, ピッピ, ポッポ}
target_pokeomo = 'ガーディ'

# 使用したいcsvファイルのパス
path = "./../csv_files/%s.csv" % target_pokeomo

# csvファイルの読み込み
heights_weights = functions.read_csv(path)

# 読み込んだcsvファイルのデータを身長と体重のデータに分離
heights, weights = functions.get_height_weight(heights_weights)

# 身長と体重の統計量の取得
height_detail, weight_detail = functions.get_details(heights, weights)
print('%sの身長の統計量' % target_pokeomo)
print(height_detail)
print('----------')
print('%sの体重の統計量' % target_pokeomo)
print(weight_detail)

```

- 結果

|  | ガーディ(身長) | ガーディ(体重) | キャタピー(身長) | キャタピー(体重) | サンド(身長) | サンド(体重) | ビリリダマ(身長) | ビリリダマ(体重) | ピッピ(身長) | ピッピ(体重) | ポッポ(身長) | ポッポ(体重) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 個数 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| 平均 | 0.683500 | 18.596900 | 0.289100 | 2.843500 | 0.593600 | 12.250700 | 0.475600 | 9.799000 | 0.586100 | 7.324900 | 0.295200 | 1.789300 |
| 標準偏差 | 0.185638 | 5.488274 | 0.083073 | 0.928569 | 0.157137 | 3.626043 | 0.118376 | 2.721827 | 0.159081 | 2.142125 | 0.079816 | 0.556329 |
| 最小値 | 0.430000 | 10.110000 | 0.180000 | 1.520000 | 0.360000 | 6.370000 | 0.310000 | 5.700000 | 0.380000 | 3.930000 | 0.180000 | 0.970000 |
| 第一四分位数 | 0.500000 | 14.667500 | 0.220000 | 2.027500 | 0.457500 | 9.267500 | 0.360000 | 7.650000 | 0.440000 | 5.492500 | 0.220000 | 1.280000 |
| 中央値 | 0.610000 | 18.570000 | 0.240000 | 2.560000 | 0.520000 | 11.580000 | 0.450000 | 9.380000 | 0.520000 | 6.950000 | 0.260000 | 1.735000 |
| 第三四分位数 | 0.870000 | 21.447500 | 0.380000 | 3.595000 | 0.760000 | 15.517500 | 0.600000 | 11.397500 | 0.740000 | 8.825000 | 0.370000 | 2.295000 |
| 最大値 | 0.970000 | 30.350000 | 0.410000 | 4.580000 | 0.830000 | 19.430000 | 0.690000 | 15.690000 | 0.840000 | 12.020000 | 0.420000 | 2.820000 |
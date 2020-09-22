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
Pythonで以下のコードを書くと様々な統計量が得られます。  

'''python:functions.py

import csv
import numpy as np
import pandas as pd

"""
    csvファイルの読み込み
"""
def read_csv(filename):
    with open(filename, 'r') as csv_file:
        data = list(csv.reader(csv_file))
    
    return data

"""
    csvファイルへの書き込み
"""
def write_csv(filename, data):
    with open(filename, 'w') as csv_file:
        writer = csv.writer(csv_file)
        for row in data:
            writer.writerow(row)

"""
    list内の要素をstringからfloatに変換
"""
def translate_to_float(string_list):
    float_list = []
    for element in string_list:
        float_list.append(float(element))
    return float_list

"""
    データの整形
    1. １行目の消去
    2. numpy配列への変換
    3. 転置
    4. string型からfloat型への変換+身長と体重を分ける
"""
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

"""
    pandasを用いて身長と体重の様々な統計量を返す。
"""
def get_details(heights, weights):
    # 身長と体重のリストをpandasのデータフレームに変換
    height_data = pd.DataFrame(heights)
    weight_data = pd.DataFrame(weights)
    # 身長と体重の統計量を取得
    height_detail = height_data.describe()
    weight_detail = weight_data.describe()

    return height_detail, weight_detail

'''
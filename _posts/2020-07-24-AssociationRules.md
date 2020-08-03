---
title: "Association Rules"
date: 2020-07-24 08:26:28 -0400
classes: wide
toc: true
categories: DataAnalysis
---


# Association Rules

<b>X (Antecedent) => Y (Consequence)</b><br>
- Support is the relative frequency that the rules show up. In many instances, you may want to look for high support in order to make sure it is a useful relationship. However, there may be instances where a low support is useful if you are trying to find “hidden” relationships.
<a href="https://www.codecogs.com/eqnedit.php?latex=Support&space;=&space;P(X\cap&space;Y)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Support&space;=&space;P(X\cap&space;Y)" title="Support = P(X\cap Y)" /></a>

- Confidence is a measure of the reliability of the rule. A confidence of .5 in the above example would mean that in 50% of the cases where Diaper and Gum were purchased, the purchase also included Beer and Chips. For product recommendation, a 50% confidence may be perfectly acceptable but in a medical situation, this level may not be high enough.
<a href="https://www.codecogs.com/eqnedit.php?latex=Confidence&space;=&space;\frac{P(X\cap&space;Y)}{P(X)}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Confidence&space;=&space;\frac{P(X\cap&space;Y)}{P(X)}" title="Confidence = \frac{P(X\cap Y)}{P(X)}" /></a>
- Lift is the ratio of the observed support to that expected if the two rules were independent. The basic rule of thumb is that a lift value close to 1 means the rules were completely independent. (P(Y)=P(Y|X)) Lift values > 1 are generally indicate that X and Y are not independent.
<a href="https://www.codecogs.com/eqnedit.php?latex=Lift&space;=&space;\frac{P(X\cap&space;Y)}{P(X)P(Y)}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Lift&space;=&space;\frac{P(X\cap&space;Y)}{P(X)P(Y)}" title="Lift = \frac{P(X\cap Y)}{P(X)P(Y)}" /></a>
- Conviction is the ratio of the expected frequency that X occurs without Y (that is to say, the frequency that the rule makes an incorrect prediction). The conviction value of 1.2 shows that the rule would be incorrect 20% more often (1.2 times as often) if the association between X and Y was purely random chance.
<a href="https://www.codecogs.com/eqnedit.php?latex=Conviction&space;=&space;\frac{1-P(Y)}{1-Confidence(X,Y))}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Conviction&space;=&space;\frac{1-P(Y)}{1-Confidence(X,Y))}" title="Conviction = \frac{1-P(Y)}{1-Confidence(X,Y))}" /></a>

https://pbpython.com/market-basket-analysis.html<br>
https://en.wikipedia.org/wiki/Association_rule_learning

# Example Codes

## Load Data

Data Source : https://arena.kakao.com/c/7
<i>Will only show part of the dataset for data protection.


```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.cm as cm 
import numpy as np
import math
import random
import networkx as nx
from mlxtend.frequent_patterns import apriori
from mlxtend.frequent_patterns import association_rules
from mlxtend.preprocessing import TransactionEncoder
from ast import literal_eval
import seaborn as sns
import matplotlib.font_manager as fm
font_location = 'C:/WINDOWS\/Fonts\/KBIZ한마음고딕 R.ttf'
font_name = fm.FontProperties(fname = font_location).get_name()
import warnings
warnings.filterwarnings('ignore')
```


```python
playlist = pd.read_csv('./market_basket/playlist.csv')
playlist.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tags</th>
      <th>id</th>
      <th>plylst_title</th>
      <th>songs</th>
      <th>like_cnt</th>
      <th>updt_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>['kpop', '댄스', '걸그룹댄스', '스트레스해소']</td>
      <td>31804</td>
      <td>걸그룹 땐쓰쏭</td>
      <td>[113664, 375431, 455945, 342798, 380564, 51842...</td>
      <td>74</td>
      <td>2020-04-13 23:36:55.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>['80년대', '감성', '90년대발라드', '올드송', '추억', '휴식', '...</td>
      <td>131098</td>
      <td>8~90년대 우리나라 라디오에서 흘러나오던 명곡들.</td>
      <td>[481669, 635537, 346897, 61595, 243099, 370203...</td>
      <td>53</td>
      <td>2019-11-25 17:25:12.000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>['기분전환', '설렘', '사랑']</td>
      <td>124665</td>
      <td>여자가 좋아하는 남자노래방곡들~</td>
      <td>[104450, 663564, 625933, 486938, 243099, 23951...</td>
      <td>83</td>
      <td>2017-11-28 11:44:44.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>['조용한', '밤', '새벽']</td>
      <td>8455</td>
      <td>혼자남겨진밤..</td>
      <td>[169984, 413314, 118788, 54408, 377355, 655756...</td>
      <td>0</td>
      <td>2017-07-25 19:45:23.000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>['포근함', '봄', '따스한', '벚꽃', '힐링', '사랑', '신나는']</td>
      <td>125944</td>
      <td>''흔들리는 벚꽃 속에서'' 들어야하는 봄 노래</td>
      <td>[132994, 204547, 283138, 243850, 257434, 58178...</td>
      <td>49</td>
      <td>2020-04-03 08:12:36.000</td>
    </tr>
  </tbody>
</table>
</div>



Each row contains one playlist. Playlists with low like counts can be considered less popular.


```python
ax =sns.boxplot(y = playlist.like_cnt)
ax.set_ylim(0,200)
```




    (0.0, 200.0)




![png](AssociationRules_files/AssociationRules_8_1.png)


Playlists up until the 3rd quantile have like counts lower than 25. Delete playlist with like counts lower than 10.


```python
top_playlist = playlist[playlist.like_cnt > 10]
top_playlist.shape
```




    (3981, 6)



3981 playlists were left.


```python
top_playlist.songs = top_playlist.songs.apply(literal_eval)
```

Map the song codes with metadata of songs.


```python
info = pd.read_csv('./market_basket/songInfo.csv')
info.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>song_gn_dtl_gnr_basket</th>
      <th>issue_date</th>
      <th>album_name</th>
      <th>album_id</th>
      <th>artist_id_basket</th>
      <th>song_name</th>
      <th>song_gn_gnr_basket</th>
      <th>artist_name_basket</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>['GN0401', 'GN0403']</td>
      <td>20150205</td>
      <td>브라운 아이드 소울 싱글 프로젝트 1st. 같은 시간 속의 너 By 나얼</td>
      <td>2302870</td>
      <td>[3959]</td>
      <td>같은 시간 속의 너</td>
      <td>['GN0400']</td>
      <td>['나얼']</td>
      <td>169984</td>
    </tr>
    <tr>
      <th>1</th>
      <td>['GN0104', 'GN0101']</td>
      <td>20070419</td>
      <td>나무로 만든 노래</td>
      <td>350016</td>
      <td>[1088]</td>
      <td>다행이다</td>
      <td>['GN0100']</td>
      <td>['이적']</td>
      <td>104450</td>
    </tr>
    <tr>
      <th>2</th>
      <td>['GN0105', 'GN0101', 'GN2505', 'GN2503', 'GN15...</td>
      <td>20161231</td>
      <td>도깨비 OST Part.7</td>
      <td>10027253</td>
      <td>[490981]</td>
      <td>I Miss You</td>
      <td>['GN2500', 'GN1500', 'GN0100']</td>
      <td>['소유 (SOYOU)']</td>
      <td>608260</td>
    </tr>
    <tr>
      <th>3</th>
      <td>['GN2704', 'GN1102', 'GN1101']</td>
      <td>20150615</td>
      <td>Roses (Feat. ROZES)</td>
      <td>2324717</td>
      <td>[713297]</td>
      <td>Roses (Feat. ROZES)</td>
      <td>['GN2700', 'GN1100']</td>
      <td>['The Chainsmokers']</td>
      <td>501764</td>
    </tr>
    <tr>
      <th>4</th>
      <td>['GN0105', 'GN0101']</td>
      <td>20161129</td>
      <td>목소리</td>
      <td>10018979</td>
      <td>[792091]</td>
      <td>이 바보야</td>
      <td>['GN0100']</td>
      <td>['정승환']</td>
      <td>118788</td>
    </tr>
  </tbody>
</table>
</div>




```python
info.shape
```




    (1003, 9)




```python
info.artist_name_basket = info.artist_name_basket.apply(literal_eval)
```


```python
info.song_gn_gnr_basket = info.song_gn_gnr_basket.apply(literal_eval)
```

map title, artist and genre.


```python
name = {}
artist ={}
genre = {}

for i in range(1003):
    artist[info.id[i]] = info.artist_name_basket[i]
    name[info.id[i]] = info.song_name[i]
    genre[info.id[i]] = info.song_gn_gnr_basket[i]
```


```python
data = [[name[song] for song in x] for x in top_playlist.songs]
```

Use transaction encoder to encode the dataframe into a sparse data format.


```python
te = TransactionEncoder()
te_ary = te.fit(data).transform(data)
df = pd.DataFrame(te_ary, columns=te.columns_)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>#Selfie</th>
      <th>...사랑했잖아...</th>
      <th>11:11</th>
      <th>12월의 기적 (Miracles In December)</th>
      <th>180도</th>
      <th>1월부터 6월까지</th>
      <th>200%</th>
      <th>2002</th>
      <th>21</th>
      <th>24K Magic</th>
      <th>...</th>
      <th>헤어진 우리가 지켜야 할 것들</th>
      <th>헤픈엔딩 (Feat. 조원선 Of 롤러코스터)</th>
      <th>혼자</th>
      <th>혼자라고 생각말기</th>
      <th>홀로 (Feat. 김나영)</th>
      <th>후회 (Feat. 박은옥)</th>
      <th>흔들리는 꽃들 속에서 네 샴푸향이 느껴진거야</th>
      <th>흔한 이별</th>
      <th>희재</th>
      <th>힘 내! (Way To Go)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3976</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3977</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3978</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3979</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3980</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>3981 rows × 963 columns</p>
</div>



## Association Rules

### BY TITLE


```python
frequent_itemsets = apriori(df, min_support=0.03, use_colnames=True,max_len=2)
frequent_itemsets
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.089425</td>
      <td>(...사랑했잖아...)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.043708</td>
      <td>(11:11)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.050239</td>
      <td>(1월부터 6월까지)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.059282</td>
      <td>(200%)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.031399</td>
      <td>(21)</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>804</th>
      <td>0.031902</td>
      <td>(오늘부터 우리는 (Me Gustas Tu), 유리구슬 (Glass Bead))</td>
    </tr>
    <tr>
      <th>805</th>
      <td>0.041698</td>
      <td>(오늘부터 우리는 (Me Gustas Tu), 음오아예 (Um Oh Ah Yeh))</td>
    </tr>
    <tr>
      <th>806</th>
      <td>0.030897</td>
      <td>(위아래, 음오아예 (Um Oh Ah Yeh))</td>
    </tr>
    <tr>
      <th>807</th>
      <td>0.030646</td>
      <td>(체념, 응급실)</td>
    </tr>
    <tr>
      <th>808</th>
      <td>0.031902</td>
      <td>(체념, 친구라도 될 걸 그랬어)</td>
    </tr>
  </tbody>
</table>
<p>809 rows × 2 columns</p>
</div>



With a min support of 0.03 and max length of 2, 809 rows were filtered.


```python
rules = association_rules(frequent_itemsets, metric="lift", min_threshold=5)
print(rules)
```

                   antecedents                consequents  antecedent support  \
    0                     (체념)              (...사랑했잖아...)            0.080131   
    1            (...사랑했잖아...)                       (체념)            0.089425   
    2     (Bad Girl Good Girl)              (Abracadabra)            0.060286   
    3            (Abracadabra)       (Bad Girl Good Girl)            0.059784   
    4                    (Gee)              (Abracadabra)            0.069329   
    ..                     ...                        ...                 ...   
    385  (음오아예 (Um Oh Ah Yeh))  (오늘부터 우리는 (Me Gustas Tu))            0.070836   
    386                  (위아래)      (음오아예 (Um Oh Ah Yeh))            0.062296   
    387  (음오아예 (Um Oh Ah Yeh))                      (위아래)            0.070836   
    388                   (체념)             (친구라도 될 걸 그랬어)            0.080131   
    389         (친구라도 될 걸 그랬어)                       (체념)            0.079126   
    
         consequent support   support  confidence      lift  leverage  conviction  
    0              0.089425  0.036172    0.451411  5.047938  0.029006    1.659849  
    1              0.080131  0.036172    0.404494  5.047938  0.029006    1.544686  
    2              0.059784  0.031148    0.516667  8.642227  0.027544    1.945275  
    3              0.060286  0.031148    0.521008  8.642227  0.027544    1.961858  
    4              0.059784  0.035669    0.514493  8.605864  0.031525    1.936564  
    ..                  ...       ...         ...       ...       ...         ...  
    385            0.082140  0.041698    0.588652  7.166439  0.035880    2.231349  
    386            0.070836  0.030897    0.495968  7.001587  0.026484    1.843460  
    387            0.062296  0.030897    0.436170  7.001587  0.026484    1.663098  
    388            0.079126  0.031902    0.398119  5.031467  0.025561    1.529994  
    389            0.080131  0.031902    0.403175  5.031467  0.025561    1.541271  
    
    [390 rows x 9 columns]
    

Make rules with min lift threshold 5 and print top 30 rules(sorted by lift).


```python
rules[:30]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(체념)</td>
      <td>(...사랑했잖아...)</td>
      <td>0.080131</td>
      <td>0.089425</td>
      <td>0.036172</td>
      <td>0.451411</td>
      <td>5.047938</td>
      <td>0.029006</td>
      <td>1.659849</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(...사랑했잖아...)</td>
      <td>(체념)</td>
      <td>0.089425</td>
      <td>0.080131</td>
      <td>0.036172</td>
      <td>0.404494</td>
      <td>5.047938</td>
      <td>0.029006</td>
      <td>1.544686</td>
    </tr>
    <tr>
      <th>2</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(Abracadabra)</td>
      <td>0.060286</td>
      <td>0.059784</td>
      <td>0.031148</td>
      <td>0.516667</td>
      <td>8.642227</td>
      <td>0.027544</td>
      <td>1.945275</td>
    </tr>
    <tr>
      <th>3</th>
      <td>(Abracadabra)</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.059784</td>
      <td>0.060286</td>
      <td>0.031148</td>
      <td>0.521008</td>
      <td>8.642227</td>
      <td>0.027544</td>
      <td>1.961858</td>
    </tr>
    <tr>
      <th>4</th>
      <td>(Gee)</td>
      <td>(Abracadabra)</td>
      <td>0.069329</td>
      <td>0.059784</td>
      <td>0.035669</td>
      <td>0.514493</td>
      <td>8.605864</td>
      <td>0.031525</td>
      <td>1.936564</td>
    </tr>
    <tr>
      <th>5</th>
      <td>(Abracadabra)</td>
      <td>(Gee)</td>
      <td>0.059784</td>
      <td>0.069329</td>
      <td>0.035669</td>
      <td>0.596639</td>
      <td>8.605864</td>
      <td>0.031525</td>
      <td>2.307288</td>
    </tr>
    <tr>
      <th>6</th>
      <td>(I Don`t Care)</td>
      <td>(Abracadabra)</td>
      <td>0.069329</td>
      <td>0.059784</td>
      <td>0.037930</td>
      <td>0.547101</td>
      <td>9.151306</td>
      <td>0.033785</td>
      <td>2.075997</td>
    </tr>
    <tr>
      <th>7</th>
      <td>(Abracadabra)</td>
      <td>(I Don`t Care)</td>
      <td>0.059784</td>
      <td>0.069329</td>
      <td>0.037930</td>
      <td>0.634454</td>
      <td>9.151306</td>
      <td>0.033785</td>
      <td>2.545973</td>
    </tr>
    <tr>
      <th>8</th>
      <td>(Abracadabra)</td>
      <td>(Nobody)</td>
      <td>0.059784</td>
      <td>0.059784</td>
      <td>0.032404</td>
      <td>0.542017</td>
      <td>9.066256</td>
      <td>0.028830</td>
      <td>2.052949</td>
    </tr>
    <tr>
      <th>9</th>
      <td>(Nobody)</td>
      <td>(Abracadabra)</td>
      <td>0.059784</td>
      <td>0.059784</td>
      <td>0.032404</td>
      <td>0.542017</td>
      <td>9.066256</td>
      <td>0.028830</td>
      <td>2.052949</td>
    </tr>
    <tr>
      <th>10</th>
      <td>(So Hot)</td>
      <td>(Abracadabra)</td>
      <td>0.062045</td>
      <td>0.059784</td>
      <td>0.034162</td>
      <td>0.550607</td>
      <td>9.209948</td>
      <td>0.030453</td>
      <td>2.092192</td>
    </tr>
    <tr>
      <th>11</th>
      <td>(Abracadabra)</td>
      <td>(So Hot)</td>
      <td>0.059784</td>
      <td>0.062045</td>
      <td>0.034162</td>
      <td>0.571429</td>
      <td>9.209948</td>
      <td>0.030453</td>
      <td>2.188562</td>
    </tr>
    <tr>
      <th>12</th>
      <td>(Tell me)</td>
      <td>(Abracadabra)</td>
      <td>0.062296</td>
      <td>0.059784</td>
      <td>0.034162</td>
      <td>0.548387</td>
      <td>9.172811</td>
      <td>0.030438</td>
      <td>2.081907</td>
    </tr>
    <tr>
      <th>13</th>
      <td>(Abracadabra)</td>
      <td>(Tell me)</td>
      <td>0.059784</td>
      <td>0.062296</td>
      <td>0.034162</td>
      <td>0.571429</td>
      <td>9.172811</td>
      <td>0.030438</td>
      <td>2.187976</td>
    </tr>
    <tr>
      <th>14</th>
      <td>(미스터)</td>
      <td>(Abracadabra)</td>
      <td>0.048731</td>
      <td>0.059784</td>
      <td>0.032906</td>
      <td>0.675258</td>
      <td>11.294962</td>
      <td>0.029993</td>
      <td>2.895268</td>
    </tr>
    <tr>
      <th>15</th>
      <td>(Abracadabra)</td>
      <td>(미스터)</td>
      <td>0.059784</td>
      <td>0.048731</td>
      <td>0.032906</td>
      <td>0.550420</td>
      <td>11.294962</td>
      <td>0.029993</td>
      <td>2.115906</td>
    </tr>
    <tr>
      <th>16</th>
      <td>(오늘부터 우리는 (Me Gustas Tu))</td>
      <td>(Ah-Choo)</td>
      <td>0.082140</td>
      <td>0.049987</td>
      <td>0.036674</td>
      <td>0.446483</td>
      <td>8.931907</td>
      <td>0.032568</td>
      <td>1.716321</td>
    </tr>
    <tr>
      <th>17</th>
      <td>(Ah-Choo)</td>
      <td>(오늘부터 우리는 (Me Gustas Tu))</td>
      <td>0.049987</td>
      <td>0.082140</td>
      <td>0.036674</td>
      <td>0.733668</td>
      <td>8.931907</td>
      <td>0.032568</td>
      <td>3.446304</td>
    </tr>
    <tr>
      <th>18</th>
      <td>(썸 (Feat. 릴보이 Of 긱스))</td>
      <td>(All For You)</td>
      <td>0.087164</td>
      <td>0.069581</td>
      <td>0.031650</td>
      <td>0.363112</td>
      <td>5.218594</td>
      <td>0.025585</td>
      <td>1.460885</td>
    </tr>
    <tr>
      <th>19</th>
      <td>(All For You)</td>
      <td>(썸 (Feat. 릴보이 Of 긱스))</td>
      <td>0.069581</td>
      <td>0.087164</td>
      <td>0.031650</td>
      <td>0.454874</td>
      <td>5.218594</td>
      <td>0.025585</td>
      <td>1.674540</td>
    </tr>
    <tr>
      <th>20</th>
      <td>(Gee)</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.069329</td>
      <td>0.060286</td>
      <td>0.033911</td>
      <td>0.489130</td>
      <td>8.113451</td>
      <td>0.029731</td>
      <td>1.839439</td>
    </tr>
    <tr>
      <th>21</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(Gee)</td>
      <td>0.060286</td>
      <td>0.069329</td>
      <td>0.033911</td>
      <td>0.562500</td>
      <td>8.113451</td>
      <td>0.029731</td>
      <td>2.127247</td>
    </tr>
    <tr>
      <th>22</th>
      <td>(I Don`t Care)</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.069329</td>
      <td>0.060286</td>
      <td>0.030897</td>
      <td>0.445652</td>
      <td>7.392255</td>
      <td>0.026717</td>
      <td>1.695170</td>
    </tr>
    <tr>
      <th>23</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(I Don`t Care)</td>
      <td>0.060286</td>
      <td>0.069329</td>
      <td>0.030897</td>
      <td>0.512500</td>
      <td>7.392255</td>
      <td>0.026717</td>
      <td>1.909068</td>
    </tr>
    <tr>
      <th>24</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(NU 예삐오 (NU ABO))</td>
      <td>0.060286</td>
      <td>0.050992</td>
      <td>0.031148</td>
      <td>0.516667</td>
      <td>10.132266</td>
      <td>0.028074</td>
      <td>1.963464</td>
    </tr>
    <tr>
      <th>25</th>
      <td>(NU 예삐오 (NU ABO))</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.050992</td>
      <td>0.060286</td>
      <td>0.031148</td>
      <td>0.610837</td>
      <td>10.132266</td>
      <td>0.028074</td>
      <td>2.414707</td>
    </tr>
    <tr>
      <th>26</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(Nobody)</td>
      <td>0.060286</td>
      <td>0.059784</td>
      <td>0.032404</td>
      <td>0.537500</td>
      <td>8.990704</td>
      <td>0.028800</td>
      <td>2.032900</td>
    </tr>
    <tr>
      <th>27</th>
      <td>(Nobody)</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.059784</td>
      <td>0.060286</td>
      <td>0.032404</td>
      <td>0.542017</td>
      <td>8.990704</td>
      <td>0.028800</td>
      <td>2.051852</td>
    </tr>
    <tr>
      <th>28</th>
      <td>(Bad Girl Good Girl)</td>
      <td>(Oh!)</td>
      <td>0.060286</td>
      <td>0.055011</td>
      <td>0.033157</td>
      <td>0.550000</td>
      <td>9.997945</td>
      <td>0.029841</td>
      <td>2.099975</td>
    </tr>
    <tr>
      <th>29</th>
      <td>(Oh!)</td>
      <td>(Bad Girl Good Girl)</td>
      <td>0.055011</td>
      <td>0.060286</td>
      <td>0.033157</td>
      <td>0.602740</td>
      <td>9.997945</td>
      <td>0.029841</td>
      <td>2.365486</td>
    </tr>
  </tbody>
</table>
</div>




```python
#visualization
def draw_graph(rules, rules_to_show):
    plt.figure(figsize=(20,20))
    G1 = nx.DiGraph() #데이터 넣기
   
    color_map=[]  
    strs=['R'+str(x) for x in range(0,rules_to_show)]   
   
   
    for i in range (rules_to_show):      
        G1.add_nodes_from(["R"+str(i)], weight = 100)
    
     
        for a in rules.iloc[i]['antecedents']:
                
            G1.add_nodes_from([a])
        
            G1.add_edge(a, "R"+str(i), color="black" , weight = 2)
       
        for c in rules.iloc[i]['consequents']:
             
            G1.add_nodes_from([c])
            
            G1.add_edge("R"+str(i), c, color="black",  weight=2) #화살표
 
    edges = G1.edges()
    colors = [G1[u][v]['color'] for u,v in edges]
    weights = [G1[u][v]['weight'] for u,v in edges]

    node_size = []
    for i in range(rules_to_show):
        node_size.append(rules.iloc[i]['support'])
    maxconf = max(node_size)
    multifier = 500/maxconf
    for i in range(rules_to_show):
        node_size[i] = node_size[i]*multifier
    size = []
    counter = 0
    for node in G1.nodes():
        if node in strs:
            size.append(node_size[counter])
            counter += 1
        else:
            size.append(0)



    cmap = cm.get_cmap('OrRd')
    node_color = []
    for i in range(rules_to_show):
        node_color.append(rules.iloc[i]['lift'])
    maxconf = max(node_color)
    multifier = 1/maxconf
    for i in range(rules_to_show):
        node_color[i] = cmap(node_color[i]*multifier)
    col = []
    counter = 0
    for node in G1.nodes():
        if node in strs:
            col.append(node_color[counter])
            counter += 1
        else:
            col.append(cmap(0))


    pos = nx.spring_layout(G1, dim=2, k=1.5/math.sqrt(rules_to_show), pos=None, fixed=None, iterations=50, weight='weight', scale=1.0)
    nx.draw(G1, pos, edges=edges, node_color = col, edge_color=colors, width=weights, font_size=16, with_labels=False,node_size=size)            



    labels = {}    
    for node in G1.nodes():
        if node not in strs:
            labels[node] = node
    nx.draw_networkx_labels(G1,pos,labels,font_size=13,font_color='r',font_family=font_name)
    plt.show()
```


```python
draw_graph(rules,25) #color = lift, size = support
```


![png](AssociationRules_files/AssociationRules_31_0.png)


The colors represent lift, the sizes represent support. Each node represents a rule.<br>
By observing the graph, we can see that songs connected share genre and time. 

### BY ARTIST


```python
data_artist = []
for x in top_playlist.songs:
    artist_list = []
    for song in x:
        for i in artist[song]:
            artist_list.append(i)
    artist_list = list(set(artist_list))
    data_artist.append(artist_list)
```


```python
te_2 = TransactionEncoder()
te_ary_2 = te.fit(data_artist).transform(data_artist)
df2 = pd.DataFrame(te_ary_2, columns=te.columns_)
frequent_itemsets2 = apriori(df2, min_support=0.03, use_colnames=True,max_len=2)
frequent_itemsets2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.233861</td>
      <td>(10CM)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.034665</td>
      <td>(2LSON)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.114795</td>
      <td>(2NE1)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.104748</td>
      <td>(40)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.033911</td>
      <td>(406호 프로젝트)</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2670</th>
      <td>0.031399</td>
      <td>(허각, 프라이머리)</td>
    </tr>
    <tr>
      <th>2671</th>
      <td>0.030394</td>
      <td>(헤이즈 (Heize), 프라이머리)</td>
    </tr>
    <tr>
      <th>2672</th>
      <td>0.041447</td>
      <td>(하동균, 허각)</td>
    </tr>
    <tr>
      <th>2673</th>
      <td>0.037679</td>
      <td>(허각, 한동근)</td>
    </tr>
    <tr>
      <th>2674</th>
      <td>0.032906</td>
      <td>(헤이즈 (Heize), 한동근)</td>
    </tr>
  </tbody>
</table>
<p>2675 rows × 2 columns</p>
</div>




```python
rules2 = association_rules(frequent_itemsets2, metric="lift", min_threshold=6)
print(rules2)
```

             antecedents      consequents  antecedent support  consequent support  \
    0             (2NE1)        (4minute)            0.114795            0.074102   
    1          (4minute)           (2NE1)            0.074102            0.114795   
    2             (2NE1)           (미쓰에이)            0.114795            0.060286   
    3             (미쓰에이)           (2NE1)            0.060286            0.114795   
    4             (원더걸스)           (2NE1)            0.110023            0.114795   
    ..               ...              ...                 ...                 ...   
    117             (하울)             (제이)            0.049485            0.084903   
    118  (찬열 (CHANYEOL))     (펀치 (Punch))            0.031650            0.093946   
    119     (펀치 (Punch))  (찬열 (CHANYEOL))            0.093946            0.031650   
    120   (창모 (CHANGMO))             (효린)            0.054760            0.079126   
    121             (효린)   (창모 (CHANGMO))            0.079126            0.054760   
    
          support  confidence       lift  leverage  conviction  
    0    0.056016    0.487965   6.585046  0.047510    1.808271  
    1    0.056016    0.755932   6.585046  0.047510    3.626880  
    2    0.047978    0.417943   6.932631  0.041057    1.614470  
    3    0.047978    0.795833   6.932631  0.041057    4.335697  
    4    0.076112    0.691781   6.026213  0.063481    2.871998  
    ..        ...         ...        ...       ...         ...  
    117  0.049485    1.000000  11.778107  0.045284         inf  
    118  0.031650    1.000000  10.644385  0.028677         inf  
    119  0.031650    0.336898  10.644385  0.028677    1.460334  
    120  0.031902    0.582569   7.362560  0.027569    2.206050  
    121  0.031902    0.403175   7.362560  0.027569    1.583780  
    
    [122 rows x 9 columns]
    


```python
rules2[:50]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(2NE1)</td>
      <td>(4minute)</td>
      <td>0.114795</td>
      <td>0.074102</td>
      <td>0.056016</td>
      <td>0.487965</td>
      <td>6.585046</td>
      <td>0.047510</td>
      <td>1.808271</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(4minute)</td>
      <td>(2NE1)</td>
      <td>0.074102</td>
      <td>0.114795</td>
      <td>0.056016</td>
      <td>0.755932</td>
      <td>6.585046</td>
      <td>0.047510</td>
      <td>3.626880</td>
    </tr>
    <tr>
      <th>2</th>
      <td>(2NE1)</td>
      <td>(미쓰에이)</td>
      <td>0.114795</td>
      <td>0.060286</td>
      <td>0.047978</td>
      <td>0.417943</td>
      <td>6.932631</td>
      <td>0.041057</td>
      <td>1.614470</td>
    </tr>
    <tr>
      <th>3</th>
      <td>(미쓰에이)</td>
      <td>(2NE1)</td>
      <td>0.060286</td>
      <td>0.114795</td>
      <td>0.047978</td>
      <td>0.795833</td>
      <td>6.932631</td>
      <td>0.041057</td>
      <td>4.335697</td>
    </tr>
    <tr>
      <th>4</th>
      <td>(원더걸스)</td>
      <td>(2NE1)</td>
      <td>0.110023</td>
      <td>0.114795</td>
      <td>0.076112</td>
      <td>0.691781</td>
      <td>6.026213</td>
      <td>0.063481</td>
      <td>2.871998</td>
    </tr>
    <tr>
      <th>5</th>
      <td>(2NE1)</td>
      <td>(원더걸스)</td>
      <td>0.114795</td>
      <td>0.110023</td>
      <td>0.076112</td>
      <td>0.663020</td>
      <td>6.026213</td>
      <td>0.063481</td>
      <td>2.641037</td>
    </tr>
    <tr>
      <th>6</th>
      <td>(카라)</td>
      <td>(2NE1)</td>
      <td>0.048731</td>
      <td>0.114795</td>
      <td>0.041698</td>
      <td>0.855670</td>
      <td>7.453879</td>
      <td>0.036104</td>
      <td>6.133204</td>
    </tr>
    <tr>
      <th>7</th>
      <td>(2NE1)</td>
      <td>(카라)</td>
      <td>0.114795</td>
      <td>0.048731</td>
      <td>0.041698</td>
      <td>0.363239</td>
      <td>7.453879</td>
      <td>0.036104</td>
      <td>1.493917</td>
    </tr>
    <tr>
      <th>8</th>
      <td>(2NE1)</td>
      <td>(티아라)</td>
      <td>0.114795</td>
      <td>0.057523</td>
      <td>0.043205</td>
      <td>0.376368</td>
      <td>6.542880</td>
      <td>0.036602</td>
      <td>1.511270</td>
    </tr>
    <tr>
      <th>9</th>
      <td>(티아라)</td>
      <td>(2NE1)</td>
      <td>0.057523</td>
      <td>0.114795</td>
      <td>0.043205</td>
      <td>0.751092</td>
      <td>6.542880</td>
      <td>0.036602</td>
      <td>3.556349</td>
    </tr>
    <tr>
      <th>10</th>
      <td>(4minute)</td>
      <td>(미쓰에이)</td>
      <td>0.074102</td>
      <td>0.060286</td>
      <td>0.036172</td>
      <td>0.488136</td>
      <td>8.096949</td>
      <td>0.031704</td>
      <td>1.835864</td>
    </tr>
    <tr>
      <th>11</th>
      <td>(미쓰에이)</td>
      <td>(4minute)</td>
      <td>0.060286</td>
      <td>0.074102</td>
      <td>0.036172</td>
      <td>0.600000</td>
      <td>8.096949</td>
      <td>0.031704</td>
      <td>2.314745</td>
    </tr>
    <tr>
      <th>12</th>
      <td>(원더걸스)</td>
      <td>(4minute)</td>
      <td>0.110023</td>
      <td>0.074102</td>
      <td>0.052499</td>
      <td>0.477169</td>
      <td>6.439355</td>
      <td>0.044346</td>
      <td>1.770932</td>
    </tr>
    <tr>
      <th>13</th>
      <td>(4minute)</td>
      <td>(원더걸스)</td>
      <td>0.074102</td>
      <td>0.110023</td>
      <td>0.052499</td>
      <td>0.708475</td>
      <td>6.439355</td>
      <td>0.044346</td>
      <td>3.052829</td>
    </tr>
    <tr>
      <th>14</th>
      <td>(카라)</td>
      <td>(4minute)</td>
      <td>0.048731</td>
      <td>0.074102</td>
      <td>0.033157</td>
      <td>0.680412</td>
      <td>9.182107</td>
      <td>0.029546</td>
      <td>2.897165</td>
    </tr>
    <tr>
      <th>15</th>
      <td>(4minute)</td>
      <td>(카라)</td>
      <td>0.074102</td>
      <td>0.048731</td>
      <td>0.033157</td>
      <td>0.447458</td>
      <td>9.182107</td>
      <td>0.029546</td>
      <td>1.721621</td>
    </tr>
    <tr>
      <th>16</th>
      <td>(4minute)</td>
      <td>(티아라)</td>
      <td>0.074102</td>
      <td>0.057523</td>
      <td>0.034916</td>
      <td>0.471186</td>
      <td>8.191237</td>
      <td>0.030653</td>
      <td>1.782248</td>
    </tr>
    <tr>
      <th>17</th>
      <td>(티아라)</td>
      <td>(4minute)</td>
      <td>0.057523</td>
      <td>0.074102</td>
      <td>0.034916</td>
      <td>0.606987</td>
      <td>8.191237</td>
      <td>0.030653</td>
      <td>2.355896</td>
    </tr>
    <tr>
      <th>18</th>
      <td>(AOA)</td>
      <td>(EXID)</td>
      <td>0.090932</td>
      <td>0.062296</td>
      <td>0.044964</td>
      <td>0.494475</td>
      <td>7.937522</td>
      <td>0.039299</td>
      <td>1.854912</td>
    </tr>
    <tr>
      <th>19</th>
      <td>(EXID)</td>
      <td>(AOA)</td>
      <td>0.062296</td>
      <td>0.090932</td>
      <td>0.044964</td>
      <td>0.721774</td>
      <td>7.937522</td>
      <td>0.039299</td>
      <td>3.267375</td>
    </tr>
    <tr>
      <th>20</th>
      <td>(걸스데이)</td>
      <td>(AOA)</td>
      <td>0.065310</td>
      <td>0.090932</td>
      <td>0.038935</td>
      <td>0.596154</td>
      <td>6.556045</td>
      <td>0.032996</td>
      <td>2.251026</td>
    </tr>
    <tr>
      <th>21</th>
      <td>(AOA)</td>
      <td>(걸스데이)</td>
      <td>0.090932</td>
      <td>0.065310</td>
      <td>0.038935</td>
      <td>0.428177</td>
      <td>6.556045</td>
      <td>0.032996</td>
      <td>1.634578</td>
    </tr>
    <tr>
      <th>22</th>
      <td>(AOA)</td>
      <td>(러블리즈)</td>
      <td>0.090932</td>
      <td>0.049987</td>
      <td>0.032404</td>
      <td>0.356354</td>
      <td>7.128863</td>
      <td>0.027858</td>
      <td>1.475985</td>
    </tr>
    <tr>
      <th>23</th>
      <td>(러블리즈)</td>
      <td>(AOA)</td>
      <td>0.049987</td>
      <td>0.090932</td>
      <td>0.032404</td>
      <td>0.648241</td>
      <td>7.128863</td>
      <td>0.027858</td>
      <td>2.584351</td>
    </tr>
    <tr>
      <th>24</th>
      <td>(Adele)</td>
      <td>(Sam Smith)</td>
      <td>0.079377</td>
      <td>0.062798</td>
      <td>0.030394</td>
      <td>0.382911</td>
      <td>6.097481</td>
      <td>0.025410</td>
      <td>1.518747</td>
    </tr>
    <tr>
      <th>25</th>
      <td>(Sam Smith)</td>
      <td>(Adele)</td>
      <td>0.062798</td>
      <td>0.079377</td>
      <td>0.030394</td>
      <td>0.484000</td>
      <td>6.097481</td>
      <td>0.025410</td>
      <td>1.784153</td>
    </tr>
    <tr>
      <th>26</th>
      <td>(Alan Walker)</td>
      <td>(The Chainsmokers)</td>
      <td>0.042954</td>
      <td>0.080884</td>
      <td>0.034916</td>
      <td>0.812865</td>
      <td>10.049744</td>
      <td>0.031442</td>
      <td>4.911525</td>
    </tr>
    <tr>
      <th>27</th>
      <td>(The Chainsmokers)</td>
      <td>(Alan Walker)</td>
      <td>0.080884</td>
      <td>0.042954</td>
      <td>0.034916</td>
      <td>0.431677</td>
      <td>10.049744</td>
      <td>0.031442</td>
      <td>1.683983</td>
    </tr>
    <tr>
      <th>28</th>
      <td>(Alessia Cara)</td>
      <td>(The Chainsmokers)</td>
      <td>0.039437</td>
      <td>0.080884</td>
      <td>0.030646</td>
      <td>0.777070</td>
      <td>9.607192</td>
      <td>0.027456</td>
      <td>4.122891</td>
    </tr>
    <tr>
      <th>29</th>
      <td>(The Chainsmokers)</td>
      <td>(Alessia Cara)</td>
      <td>0.080884</td>
      <td>0.039437</td>
      <td>0.030646</td>
      <td>0.378882</td>
      <td>9.607192</td>
      <td>0.027456</td>
      <td>1.546506</td>
    </tr>
    <tr>
      <th>30</th>
      <td>(Alessia Cara)</td>
      <td>(Zedd)</td>
      <td>0.039437</td>
      <td>0.033157</td>
      <td>0.030646</td>
      <td>0.777070</td>
      <td>23.435727</td>
      <td>0.029338</td>
      <td>4.336979</td>
    </tr>
    <tr>
      <th>31</th>
      <td>(Zedd)</td>
      <td>(Alessia Cara)</td>
      <td>0.033157</td>
      <td>0.039437</td>
      <td>0.030646</td>
      <td>0.924242</td>
      <td>23.435727</td>
      <td>0.029338</td>
      <td>12.679427</td>
    </tr>
    <tr>
      <th>32</th>
      <td>(Jessie J)</td>
      <td>(Ariana Grande)</td>
      <td>0.037930</td>
      <td>0.086159</td>
      <td>0.037930</td>
      <td>1.000000</td>
      <td>11.606414</td>
      <td>0.034662</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>33</th>
      <td>(Ariana Grande)</td>
      <td>(Jessie J)</td>
      <td>0.086159</td>
      <td>0.037930</td>
      <td>0.037930</td>
      <td>0.440233</td>
      <td>11.606414</td>
      <td>0.034662</td>
      <td>1.718698</td>
    </tr>
    <tr>
      <th>34</th>
      <td>(Nicki Minaj)</td>
      <td>(Ariana Grande)</td>
      <td>0.037930</td>
      <td>0.086159</td>
      <td>0.037930</td>
      <td>1.000000</td>
      <td>11.606414</td>
      <td>0.034662</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>35</th>
      <td>(Ariana Grande)</td>
      <td>(Nicki Minaj)</td>
      <td>0.086159</td>
      <td>0.037930</td>
      <td>0.037930</td>
      <td>0.440233</td>
      <td>11.606414</td>
      <td>0.034662</td>
      <td>1.718698</td>
    </tr>
    <tr>
      <th>36</th>
      <td>(Coldplay)</td>
      <td>(Calvin Harris)</td>
      <td>0.094197</td>
      <td>0.055011</td>
      <td>0.035167</td>
      <td>0.373333</td>
      <td>6.786484</td>
      <td>0.029985</td>
      <td>1.507961</td>
    </tr>
    <tr>
      <th>37</th>
      <td>(Calvin Harris)</td>
      <td>(Coldplay)</td>
      <td>0.055011</td>
      <td>0.094197</td>
      <td>0.035167</td>
      <td>0.639269</td>
      <td>6.786484</td>
      <td>0.029985</td>
      <td>2.511022</td>
    </tr>
    <tr>
      <th>38</th>
      <td>(The Chainsmokers)</td>
      <td>(Calvin Harris)</td>
      <td>0.080884</td>
      <td>0.055011</td>
      <td>0.040693</td>
      <td>0.503106</td>
      <td>9.145495</td>
      <td>0.036244</td>
      <td>1.901790</td>
    </tr>
    <tr>
      <th>39</th>
      <td>(Calvin Harris)</td>
      <td>(The Chainsmokers)</td>
      <td>0.055011</td>
      <td>0.080884</td>
      <td>0.040693</td>
      <td>0.739726</td>
      <td>9.145495</td>
      <td>0.036244</td>
      <td>3.531340</td>
    </tr>
    <tr>
      <th>40</th>
      <td>(Troye Sivan)</td>
      <td>(Calvin Harris)</td>
      <td>0.070836</td>
      <td>0.055011</td>
      <td>0.031148</td>
      <td>0.439716</td>
      <td>7.993199</td>
      <td>0.027251</td>
      <td>1.686625</td>
    </tr>
    <tr>
      <th>41</th>
      <td>(Calvin Harris)</td>
      <td>(Troye Sivan)</td>
      <td>0.055011</td>
      <td>0.070836</td>
      <td>0.031148</td>
      <td>0.566210</td>
      <td>7.993199</td>
      <td>0.027251</td>
      <td>2.141966</td>
    </tr>
    <tr>
      <th>42</th>
      <td>(Justin Bieber)</td>
      <td>(Charlie Puth)</td>
      <td>0.066315</td>
      <td>0.097714</td>
      <td>0.040944</td>
      <td>0.617424</td>
      <td>6.318678</td>
      <td>0.034465</td>
      <td>2.358450</td>
    </tr>
    <tr>
      <th>43</th>
      <td>(Charlie Puth)</td>
      <td>(Justin Bieber)</td>
      <td>0.097714</td>
      <td>0.066315</td>
      <td>0.040944</td>
      <td>0.419023</td>
      <td>6.318678</td>
      <td>0.034465</td>
      <td>1.607095</td>
    </tr>
    <tr>
      <th>44</th>
      <td>(Troye Sivan)</td>
      <td>(Charlie Puth)</td>
      <td>0.070836</td>
      <td>0.097714</td>
      <td>0.042200</td>
      <td>0.595745</td>
      <td>6.096811</td>
      <td>0.035279</td>
      <td>2.231970</td>
    </tr>
    <tr>
      <th>45</th>
      <td>(Charlie Puth)</td>
      <td>(Troye Sivan)</td>
      <td>0.097714</td>
      <td>0.070836</td>
      <td>0.042200</td>
      <td>0.431877</td>
      <td>6.096811</td>
      <td>0.035279</td>
      <td>1.635496</td>
    </tr>
    <tr>
      <th>46</th>
      <td>(Wiz Khalifa)</td>
      <td>(Charlie Puth)</td>
      <td>0.040191</td>
      <td>0.097714</td>
      <td>0.040191</td>
      <td>1.000000</td>
      <td>10.233933</td>
      <td>0.036264</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>47</th>
      <td>(Charlie Puth)</td>
      <td>(Wiz Khalifa)</td>
      <td>0.097714</td>
      <td>0.040191</td>
      <td>0.040191</td>
      <td>0.411311</td>
      <td>10.233933</td>
      <td>0.036264</td>
      <td>1.630418</td>
    </tr>
    <tr>
      <th>48</th>
      <td>(Coldplay)</td>
      <td>(The Chainsmokers)</td>
      <td>0.094197</td>
      <td>0.080884</td>
      <td>0.052248</td>
      <td>0.554667</td>
      <td>6.857540</td>
      <td>0.044629</td>
      <td>2.063883</td>
    </tr>
    <tr>
      <th>49</th>
      <td>(The Chainsmokers)</td>
      <td>(Coldplay)</td>
      <td>0.080884</td>
      <td>0.094197</td>
      <td>0.052248</td>
      <td>0.645963</td>
      <td>6.857540</td>
      <td>0.044629</td>
      <td>2.558495</td>
    </tr>
  </tbody>
</table>
</div>




```python
draw_graph(rules2,35)#노드가 룰
```


![png](AssociationRules_files/AssociationRules_38_0.png)


Women K-pop artists before 2015 and after 2015 are separated from each other, but connected within. Women pop artists are also connected (Jessie J, Nicki Minaj and Arana Grande). Sam Smith and Adele show up together, and this seems quite reasonable sonce they have a similar vibe. 

### BY GENRE

Map genre again since they were in codes.


```python
genre_info = pd.read_json('./market_basket/genre_gn_all.json',typ='series')
```


```python
genre_info = genre_info.to_frame().reset_index()
```


```python
genre_info.columns = ['code','genre']
```


```python
genre_dict = {}
for i in range(254):
    genre_dict[genre_info.code[i]] = genre_info.genre[i]
```


```python
data_genre = []
for x in top_playlist.songs:
    genre_list = []
    for song in x:
        for i in genre[song]:
            genre_list.append(i)
    genre_list = list(set(genre_list))
    data_genre.append(genre_list)
```


```python
data_genre = [[genre_dict[x] for x in l] for l in data_genre]
```


```python
te_3 = TransactionEncoder()
te_ary_3 = te.fit(data_genre).transform(data_genre)
df3 = pd.DataFrame(te_ary_3, columns=te.columns_)
frequent_itemsets3 = apriori(df3, min_support=0.05, use_colnames=True,max_len=3)
frequent_itemsets3
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.118563</td>
      <td>(EDM)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.728460</td>
      <td>(OST)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.363728</td>
      <td>(POP)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.874152</td>
      <td>(R&amp;B/Soul)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.509420</td>
      <td>(댄스)</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>264</th>
      <td>0.087164</td>
      <td>(일렉트로니카, 포크/블루스, 발라드)</td>
    </tr>
    <tr>
      <th>265</th>
      <td>0.085908</td>
      <td>(일렉트로니카, 아이돌, 인디음악)</td>
    </tr>
    <tr>
      <th>266</th>
      <td>0.437327</td>
      <td>(포크/블루스, 아이돌, 인디음악)</td>
    </tr>
    <tr>
      <th>267</th>
      <td>0.077619</td>
      <td>(일렉트로니카, 아이돌, 포크/블루스)</td>
    </tr>
    <tr>
      <th>268</th>
      <td>0.084903</td>
      <td>(일렉트로니카, 포크/블루스, 인디음악)</td>
    </tr>
  </tbody>
</table>
<p>269 rows × 2 columns</p>
</div>




```python
rules3 = association_rules(frequent_itemsets3, metric="lift", min_threshold=2)
print(rules3)
```

             antecedents       consequents  antecedent support  \
    0              (POP)             (EDM)            0.363728   
    1              (EDM)             (POP)            0.118563   
    2           (일렉트로니카)             (EDM)            0.182366   
    3              (EDM)          (일렉트로니카)            0.118563   
    4           (일렉트로니카)             (POP)            0.182366   
    ..               ...               ...                 ...   
    85             (POP)     (일렉트로니카, 아이돌)            0.363728   
    86    (일렉트로니카, 인디음악)             (POP)            0.102487   
    87             (POP)    (일렉트로니카, 인디음악)            0.363728   
    88  (일렉트로니카, 포크/블루스)             (POP)            0.089425   
    89             (POP)  (일렉트로니카, 포크/블루스)            0.363728   
    
        consequent support   support  confidence      lift  leverage  conviction  
    0             0.118563  0.111027    0.305249  2.574565  0.067903    1.268708  
    1             0.363728  0.111027    0.936441  2.574565  0.067903   10.010684  
    2             0.118563  0.117558    0.644628  5.437001  0.095936    2.480322  
    3             0.182366  0.117558    0.991525  5.437001  0.095936   96.480784  
    4             0.363728  0.152977    0.838843  2.306239  0.086645    3.948151  
    ..                 ...       ...         ...       ...       ...         ...  
    85            0.112283  0.089676    0.246547  2.195757  0.048835    1.178198  
    86            0.363728  0.078372    0.764706  2.102413  0.041095    2.704157  
    87            0.102487  0.078372    0.215470  2.102413  0.041095    1.144013  
    88            0.363728  0.068073    0.761236  2.092873  0.035547    2.664858  
    89            0.089425  0.068073    0.187155  2.092873  0.035547    1.120232  
    
    [90 rows x 9 columns]
    


```python
rules3[:30]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(POP)</td>
      <td>(EDM)</td>
      <td>0.363728</td>
      <td>0.118563</td>
      <td>0.111027</td>
      <td>0.305249</td>
      <td>2.574565</td>
      <td>0.067903</td>
      <td>1.268708</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(EDM)</td>
      <td>(POP)</td>
      <td>0.118563</td>
      <td>0.363728</td>
      <td>0.111027</td>
      <td>0.936441</td>
      <td>2.574565</td>
      <td>0.067903</td>
      <td>10.010684</td>
    </tr>
    <tr>
      <th>2</th>
      <td>(일렉트로니카)</td>
      <td>(EDM)</td>
      <td>0.182366</td>
      <td>0.118563</td>
      <td>0.117558</td>
      <td>0.644628</td>
      <td>5.437001</td>
      <td>0.095936</td>
      <td>2.480322</td>
    </tr>
    <tr>
      <th>3</th>
      <td>(EDM)</td>
      <td>(일렉트로니카)</td>
      <td>0.118563</td>
      <td>0.182366</td>
      <td>0.117558</td>
      <td>0.991525</td>
      <td>5.437001</td>
      <td>0.095936</td>
      <td>96.480784</td>
    </tr>
    <tr>
      <th>4</th>
      <td>(일렉트로니카)</td>
      <td>(POP)</td>
      <td>0.182366</td>
      <td>0.363728</td>
      <td>0.152977</td>
      <td>0.838843</td>
      <td>2.306239</td>
      <td>0.086645</td>
      <td>3.948151</td>
    </tr>
    <tr>
      <th>5</th>
      <td>(POP)</td>
      <td>(일렉트로니카)</td>
      <td>0.363728</td>
      <td>0.182366</td>
      <td>0.152977</td>
      <td>0.420580</td>
      <td>2.306239</td>
      <td>0.086645</td>
      <td>1.411125</td>
    </tr>
    <tr>
      <th>6</th>
      <td>(OST, EDM)</td>
      <td>(POP)</td>
      <td>0.067571</td>
      <td>0.363728</td>
      <td>0.064557</td>
      <td>0.955390</td>
      <td>2.626664</td>
      <td>0.039979</td>
      <td>14.263104</td>
    </tr>
    <tr>
      <th>7</th>
      <td>(POP)</td>
      <td>(OST, EDM)</td>
      <td>0.363728</td>
      <td>0.067571</td>
      <td>0.064557</td>
      <td>0.177486</td>
      <td>2.626664</td>
      <td>0.039979</td>
      <td>1.133633</td>
    </tr>
    <tr>
      <th>8</th>
      <td>(OST, 일렉트로니카)</td>
      <td>(EDM)</td>
      <td>0.118061</td>
      <td>0.118563</td>
      <td>0.066566</td>
      <td>0.563830</td>
      <td>4.755522</td>
      <td>0.052569</td>
      <td>2.020855</td>
    </tr>
    <tr>
      <th>9</th>
      <td>(OST, EDM)</td>
      <td>(일렉트로니카)</td>
      <td>0.067571</td>
      <td>0.182366</td>
      <td>0.066566</td>
      <td>0.985130</td>
      <td>5.401932</td>
      <td>0.054244</td>
      <td>54.985870</td>
    </tr>
    <tr>
      <th>10</th>
      <td>(일렉트로니카)</td>
      <td>(OST, EDM)</td>
      <td>0.182366</td>
      <td>0.067571</td>
      <td>0.066566</td>
      <td>0.365014</td>
      <td>5.401932</td>
      <td>0.054244</td>
      <td>1.468424</td>
    </tr>
    <tr>
      <th>11</th>
      <td>(EDM)</td>
      <td>(OST, 일렉트로니카)</td>
      <td>0.118563</td>
      <td>0.118061</td>
      <td>0.066566</td>
      <td>0.561441</td>
      <td>4.755522</td>
      <td>0.052569</td>
      <td>2.010992</td>
    </tr>
    <tr>
      <th>12</th>
      <td>(POP, R&amp;B/Soul)</td>
      <td>(EDM)</td>
      <td>0.335594</td>
      <td>0.118563</td>
      <td>0.098719</td>
      <td>0.294162</td>
      <td>2.481054</td>
      <td>0.058930</td>
      <td>1.248780</td>
    </tr>
    <tr>
      <th>13</th>
      <td>(EDM, R&amp;B/Soul)</td>
      <td>(POP)</td>
      <td>0.103743</td>
      <td>0.363728</td>
      <td>0.098719</td>
      <td>0.951574</td>
      <td>2.616171</td>
      <td>0.060985</td>
      <td>13.139023</td>
    </tr>
    <tr>
      <th>14</th>
      <td>(POP)</td>
      <td>(EDM, R&amp;B/Soul)</td>
      <td>0.363728</td>
      <td>0.103743</td>
      <td>0.098719</td>
      <td>0.271409</td>
      <td>2.616171</td>
      <td>0.060985</td>
      <td>1.230124</td>
    </tr>
    <tr>
      <th>15</th>
      <td>(EDM)</td>
      <td>(POP, R&amp;B/Soul)</td>
      <td>0.118563</td>
      <td>0.335594</td>
      <td>0.098719</td>
      <td>0.832627</td>
      <td>2.481054</td>
      <td>0.058930</td>
      <td>3.969615</td>
    </tr>
    <tr>
      <th>16</th>
      <td>(POP, 댄스)</td>
      <td>(EDM)</td>
      <td>0.184125</td>
      <td>0.118563</td>
      <td>0.050490</td>
      <td>0.274216</td>
      <td>2.312822</td>
      <td>0.028659</td>
      <td>1.214461</td>
    </tr>
    <tr>
      <th>17</th>
      <td>(EDM, 댄스)</td>
      <td>(POP)</td>
      <td>0.055262</td>
      <td>0.363728</td>
      <td>0.050490</td>
      <td>0.913636</td>
      <td>2.511869</td>
      <td>0.030389</td>
      <td>7.367363</td>
    </tr>
    <tr>
      <th>18</th>
      <td>(POP)</td>
      <td>(EDM, 댄스)</td>
      <td>0.363728</td>
      <td>0.055262</td>
      <td>0.050490</td>
      <td>0.138812</td>
      <td>2.511869</td>
      <td>0.030389</td>
      <td>1.097017</td>
    </tr>
    <tr>
      <th>19</th>
      <td>(EDM)</td>
      <td>(POP, 댄스)</td>
      <td>0.118563</td>
      <td>0.184125</td>
      <td>0.050490</td>
      <td>0.425847</td>
      <td>2.312822</td>
      <td>0.028659</td>
      <td>1.421008</td>
    </tr>
    <tr>
      <th>20</th>
      <td>(POP, 랩/힙합)</td>
      <td>(EDM)</td>
      <td>0.299925</td>
      <td>0.118563</td>
      <td>0.094700</td>
      <td>0.315745</td>
      <td>2.663098</td>
      <td>0.059140</td>
      <td>1.288171</td>
    </tr>
    <tr>
      <th>21</th>
      <td>(EDM, 랩/힙합)</td>
      <td>(POP)</td>
      <td>0.099724</td>
      <td>0.363728</td>
      <td>0.094700</td>
      <td>0.949622</td>
      <td>2.610805</td>
      <td>0.058428</td>
      <td>12.630005</td>
    </tr>
    <tr>
      <th>22</th>
      <td>(POP)</td>
      <td>(EDM, 랩/힙합)</td>
      <td>0.363728</td>
      <td>0.099724</td>
      <td>0.094700</td>
      <td>0.260359</td>
      <td>2.610805</td>
      <td>0.058428</td>
      <td>1.217180</td>
    </tr>
    <tr>
      <th>23</th>
      <td>(EDM)</td>
      <td>(POP, 랩/힙합)</td>
      <td>0.118563</td>
      <td>0.299925</td>
      <td>0.094700</td>
      <td>0.798729</td>
      <td>2.663098</td>
      <td>0.059140</td>
      <td>3.478269</td>
    </tr>
    <tr>
      <th>24</th>
      <td>(록/메탈, POP)</td>
      <td>(EDM)</td>
      <td>0.320522</td>
      <td>0.118563</td>
      <td>0.093695</td>
      <td>0.292320</td>
      <td>2.465519</td>
      <td>0.055693</td>
      <td>1.245530</td>
    </tr>
    <tr>
      <th>25</th>
      <td>(록/메탈, EDM)</td>
      <td>(POP)</td>
      <td>0.097965</td>
      <td>0.363728</td>
      <td>0.093695</td>
      <td>0.956410</td>
      <td>2.629468</td>
      <td>0.058062</td>
      <td>14.596835</td>
    </tr>
    <tr>
      <th>26</th>
      <td>(POP)</td>
      <td>(록/메탈, EDM)</td>
      <td>0.363728</td>
      <td>0.097965</td>
      <td>0.093695</td>
      <td>0.257597</td>
      <td>2.629468</td>
      <td>0.058062</td>
      <td>1.215020</td>
    </tr>
    <tr>
      <th>27</th>
      <td>(EDM)</td>
      <td>(록/메탈, POP)</td>
      <td>0.118563</td>
      <td>0.320522</td>
      <td>0.093695</td>
      <td>0.790254</td>
      <td>2.465519</td>
      <td>0.055693</td>
      <td>3.239529</td>
    </tr>
    <tr>
      <th>28</th>
      <td>(아이돌, EDM)</td>
      <td>(POP)</td>
      <td>0.064557</td>
      <td>0.363728</td>
      <td>0.059282</td>
      <td>0.918288</td>
      <td>2.524658</td>
      <td>0.035801</td>
      <td>7.786761</td>
    </tr>
    <tr>
      <th>29</th>
      <td>(POP)</td>
      <td>(아이돌, EDM)</td>
      <td>0.363728</td>
      <td>0.064557</td>
      <td>0.059282</td>
      <td>0.162983</td>
      <td>2.524658</td>
      <td>0.035801</td>
      <td>1.117592</td>
    </tr>
  </tbody>
</table>
</div>




```python
draw_graph(rules3,35)
```


![png](AssociationRules_files/AssociationRules_51_0.png)


This doesn't give us much insight because it appears quite obvious. e.g)People who listen to pop also listen more to EDM or Hip Hop. 

## Conclusion

- Used only part of data bc of computational reasons. Would have been better if there were more data.
- Rules include songs of different time zones.

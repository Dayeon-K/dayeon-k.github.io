
# Hash

Key(데이터)와 주소(데이터의 위치)를 매핑시키는 것을 통해 구현한 자료구조로, O(1)의 복잡도로 삽입, 수정이 가능하며 중복되는 값이 없어 효율적임. <br>
[출처] [알고리즘 입문] 자료구조 - 해시테이블(Hash Table)

## 완주하지 못한 선수

수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

제한사항
- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
- completion의 길이는 participant의 길이보다 1 작습니다.
- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
- 참가자 중에는 동명이인이 있을 수 있습니다.

participant	| completion | return
- ["leo", "kiki", "eden"], ["eden", "kiki"] | "leo"
- ["marina", "josipa", "nikola", "vinko", "filipa"], ["josipa", "filipa", "marina", "nikola"] | "vinko"
- ["mislav", "stanko", "mislav", "ana"], ["stanko", "ana", "mislav"] | "mislav"


```python
#나의 풀이
def solution(participant, completion):
    d = {}
    for i in participant:
        if i in d:
            d[i] += 1
        else: d[i] =1
    for i in completion:
        if d[i] >= 2:
            d[i] -= 1
        else: del d[i]
    return list(d.keys())[0]
```


```python
#모범 풀이
import collections

def solution(participant, completion):
    answer = collections.Counter(participant) - collections.Counter(completion)  #Counter객체끼리는 뺄 수 있음
    
    return list(answer.keys())[0]
```

## 전화번호 목록

전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.

구조대 : 119
박준영 : 97 674 223
지영석 : 11 9552 4421
전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

제한 사항
- phone_book의 길이는 1 이상 1,000,000 이하입니다.
- 각 전화번호의 길이는 1 이상 20 이하입니다.

phone_book	|  return
- ["119", "97674223", "1195524421"]	| false
- ["123","456","789"]	|  true
- ["12","123","1235","567","88"]	|   false


```python
sorted(["119", "97674223", "1195524421"])
```




    ['119', '1195524421', '97674223']




```python
def solution(phone_book):
    phone_book.sort()
    for i,j in zip(phone_book,phone_book[1:]):
        if j.startswith(i):  #startswith 함수 쓰기
            return False
    return True
```

## 위장

스파이들은 매일 다른 옷을 조합하여 입어 자신을 위장합니다.

예를 들어 스파이가 가진 옷이 아래와 같고 오늘 스파이가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야 합니다.

종류 |이름
- 얼굴: 	동그란 안경, 검정 선글라스
- 상의:	파란색 티셔츠
- 하의:	청바지
- 겉옷:	긴 코트
</ul>
<br>
스파이가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.

제한사항
- clothes의 각 행은 [의상의 이름, 의상의 종류]로 이루어져 있습니다.
- 스파이가 가진 의상의 수는 1개 이상 30개 이하입니다.
- 같은 이름을 가진 의상은 존재하지 않습니다.
- clothes의 모든 원소는 문자열로 이루어져 있습니다.
- 모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.
- 스파이는 하루에 최소 한 개의 의상은 입습니다.

clothes	| return
- [["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ["green_turban", "headgear"]] |	5
- [["crow_mask", "face"], ["blue_sunglasses", "face"], ["smoky_makeup", "face"]] |	3


```python
#나의 답안
def solution(clothes):
    d = {}
    for i in clothes:
        if i[1] in d:
            d[i[1]] += 1
        else: d[i[1]] = 1
    answer = 1
    for i in d.values():
        answer = answer*(i+1)
    return answer-1
```


```python
#모범답안
import collections
from functools import reduce

def solution(c):
    return reduce(lambda x,y:x*y,[a+1 for a in collections.Counter([x[1] for x in c]).values()])-1
```


```python
#functools.reduce  
# reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates ((((1+2)+3)+4)+5)
```

## 베스트 앨범

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

- 속한 노래가 많이 재생된 장르를 먼저 수록합니다.
- 장르 내에서 많이 재생된 노래를 먼저 수록합니다.
- 장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.
- 노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

제한사항
- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.

genres |	plays	| return
- ["classic", "pop", "classic", "classic", "pop"], [500, 600, 150, 800, 2500] |	[4, 1, 3, 0]


```python
def solution(genres, plays) :
    genre_dict = {}
    plays_dict={}
    answer = []
    for i in range(len(genres)):
        if genres[i] in genre_dict:
            genre_dict[genres[i]] += plays[i]
        else: genre_dict[genres[i]] = plays[i]
        
        
    g = [x[0] for x in sorted(genre_dict.items(),key= lambda x:x[1],reverse=True)]
    
    for ind, song in enumerate(plays):
        if genres[ind] in plays_dict:
            plays_dict[genres[ind]].append((ind,song))
        else: 
            plays_dict[genres[ind]] = [(ind,song)]
   
    for i in g:

        if len(plays_dict[i]) >=2:
            answer += [x[0] for x in sorted(plays_dict[i],key= lambda x:x[1],reverse=True)[:2]]
        else:
            answer += [x[0] for x in sorted(plays_dict[i],key= lambda x:x[1],reverse=True)]
    
    return answer
```


```python
solution(["classic", "pop", "classic", "classic", "pop"], [500, 600, 150, 800, 2500] )
```




    [4, 1, 3, 0]



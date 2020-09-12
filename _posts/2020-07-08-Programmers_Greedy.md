---
title: "Programmers-Greedy"
date: 2020-07-08 08:26:28 -0400
classes: wide
toc: true
categories: [Algorithm]
---

# Greedy

# 1. 체육복

점심시간에 도둑이 들어, 일부 학생이 체육복을 도난당했습니다. 다행히 여벌 체육복이 있는 학생이 이들에게 체육복을 빌려주려 합니다. 학생들의 번호는 체격 순으로 매겨져 있어, 바로 앞번호의 학생이나 바로 뒷번호의 학생에게만 체육복을 빌려줄 수 있습니다. 예를 들어, 4번 학생은 3번 학생이나 5번 학생에게만 체육복을 빌려줄 수 있습니다. 체육복이 없으면 수업을 들을 수 없기 때문에 체육복을 적절히 빌려 최대한 많은 학생이 체육수업을 들어야 합니다.

전체 학생의 수 n, 체육복을 도난당한 학생들의 번호가 담긴 배열 lost, 여벌의 체육복을 가져온 학생들의 번호가 담긴 배열 reserve가 매개변수로 주어질 때, 체육수업을 들을 수 있는 학생의 최댓값을 return 하도록 solution 함수를 작성해주세요.

제한사항
- 전체 학생의 수는 2명 이상 30명 이하입니다.
- 체육복을 도난당한 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌의 체육복을 가져온 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌 체육복이 있는 학생만 다른 학생에게 체육복을 빌려줄 수 있습니다.
- 여벌 체육복을 가져온 학생이 체육복을 도난당했을 수 있습니다. 이때 이 학생은 체육복을 하나만 도난당했다고 가정하며, 남은 체육복이 하나이기에 다른 학생에게는 체육복을 빌려줄 수 없습니다.

[입출력 예]<br>
n,  lost,	reserve  |	return
- 5, 	[2, 4], 	[1, 3, 5]	|  5
- 5, 	[2, 4], 	[3]	 |   4
- 3, 	[3],  	[1], 	|  2


```python
def solution(n,lost,reserve):
    lost_ = [x for  x in lost if x not in reserve]
    lost_.sort()
    reserve_ = [x for x in reserve if x not in lost]
    count = 0
    while lost_:
        f = lost_.pop(0)
        if f-1 in reserve_:
            reserve_.remove(f-1)
        elif f+1 in reserve_:
            reserve_.remove(f+1)
        else:
            count += 1 
    return n - count
```


```python
solution(5,[2,4],[1,3,5])
```

    [2, 4] [1, 3, 5]





    5




```python
solution(5,[2,4],[3])
```

    [2, 4] [3]





    4




```python
# set 사용하기
lost_ = list(set(lost) - set(reserve))
reserve_ = list(set(reserve) - set(lost))
```

# 2. 큰 수 만들기

어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.

예를 들어, 숫자 1924에서 수 두 개를 제거하면 [19, 12, 14, 92, 94, 24] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.

문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.

제한 조건
- number는 1자리 이상, 1,000,000자리 이하인 숫자입니다.
- k는 1 이상 number의 자릿수 미만인 자연수입니다.


```python
'123'[4:]
```




    ''



[입출력 예] <br>
number,	k	|   return
- "1924",	2	|  "94"
- "1231234",	3	|  "3234"
- "4177252841", 	4	|  "775841"


```python
# 테스트 케이스 하나에서 시간초과
def solution(number,k):
    i = 0
    while k != 0 and i < len(number):
        if number[:i]+number[i+1:] >= number: #지웠을 때 숫자가 더 커지면
            number = number[:i]+number[i+1:] #지운 값으로 숫자 변경
            k -= 1 #k 하나 줄여줌
            if i != 0:
                i -= 1 #뒤로 돌아가서 다시 확인 417같은 경우
        else:
            i += 1 #지워도 안커지면 다음 인덱스로
            
    if k> 0: #인덱스를 다 확인했는데 k가 남아있으면
        number = number[:-k] #남아있는 만큼 뒤에서 지워버림, 777같은 경우
    return number
```


```python
def solution(number, k):
    i=0
    while k != 0 and i < len(number)-1:
        if number[i]<number[i+1]: #지울 숫자만 비교
            number = number[:i]+number[i+1:]
            if i!=0:
                i-=1
            k-=1
        else:
            i+=1
    if k>0:
        return number[:-k]
    return number
```


```python
solution("777",2)
```




    '7'




```python
solution("1924",2)
```




    '94'




```python
solution("1231234", 3)
```




    '3234'




```python
solution("4177252841", 4 )
```




    '775841'



# 3. 조이스틱

조이스틱으로 알파벳 이름을 완성하세요. 맨 처음엔 A로만 이루어져 있습니다.
ex) 완성해야 하는 이름이 세 글자면 AAA, 네 글자면 AAAA

조이스틱을 각 방향으로 움직이면 아래와 같습니다.

▲ - 다음 알파벳
▼ - 이전 알파벳 (A에서 아래쪽으로 이동하면 Z로)
◀ - 커서를 왼쪽으로 이동 (첫 번째 위치에서 왼쪽으로 이동하면 마지막 문자에 커서)
▶ - 커서를 오른쪽으로 이동
예를 들어 아래의 방법으로 JAZ를 만들 수 있습니다.

- 첫 번째 위치에서 조이스틱을 위로 9번 조작하여 J를 완성합니다.
- 조이스틱을 왼쪽으로 1번 조작하여 커서를 마지막 문자 위치로 이동시킵니다.
- 마지막 위치에서 조이스틱을 아래로 1번 조작하여 Z를 완성합니다.
따라서 11번 이동시켜 "JAZ"를 만들 수 있고, 이때가 최소 이동입니다.
만들고자 하는 이름 name이 매개변수로 주어질 때, 이름에 대해 조이스틱 조작 횟수의 최솟값을 return 하도록 solution 함수를 만드세요.

제한 사항
- name은 알파벳 대문자로만 이루어져 있습니다.
- name의 길이는 1 이상 20 이하입니다.

[입출력 예]<br>
name	|   return
- "JEROEN"	|   56
- "JAN"	 |   23


```python
def solution(name):
    count=0
    alpha='ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    d={}
    indexes=[]
    current_idx=0
    n=len(name)
    for i in range(len(alpha)):
        d[alpha[i]]=min(i,26-i) #알파벳 바꾸기 위해 필요한 수 계산

    for i in range(n):
        num=d[name[i]]
        count+=num
        if num !=0 :
            indexes.append(i) #알파벳 바꾸기 위한 횟수 추가, a가 아닌것들 index 리스트 만들기
            
    while True:
        if len(indexes)==0:
            break
        min_dist=99
        min_idx=0
        for it in indexes:
            min_dist2=min(abs(it-current_idx),n-abs(it-current_idx)) #현재 위치에서 거리 계산
            if min_dist2 < min_dist:
                min_dist=min_dist2
                min_idx=it #현재 위치에서 가장 가까운 index 찾음
        count+=min_dist #거리 더해줌
        indexes.remove(min_idx) #바꿨으니까 삭제
        current_idx = min_idx #현재 위치 update

    return count
```

# 4. 구명보트

무인도에 갇힌 사람들을 구명보트를 이용하여 구출하려고 합니다. 구명보트는 작아서 한 번에 최대 2명씩 밖에 탈 수 없고, 무게 제한도 있습니다.

예를 들어, 사람들의 몸무게가 [70kg, 50kg, 80kg, 50kg]이고 구명보트의 무게 제한이 100kg이라면 2번째 사람과 4번째 사람은 같이 탈 수 있지만 1번째 사람과 3번째 사람의 무게의 합은 150kg이므로 구명보트의 무게 제한을 초과하여 같이 탈 수 없습니다.

구명보트를 최대한 적게 사용하여 모든 사람을 구출하려고 합니다.

사람들의 몸무게를 담은 배열 people과 구명보트의 무게 제한 limit가 매개변수로 주어질 때, 모든 사람을 구출하기 위해 필요한 구명보트 개수의 최솟값을 return 하도록 solution 함수를 작성해주세요.

제한사항
- 무인도에 갇힌 사람은 1명 이상 50,000명 이하입니다.
- 각 사람의 몸무게는 40kg 이상 240kg 이하입니다.
- 구명보트의 무게 제한은 40kg 이상 240kg 이하입니다.
- 구명보트의 무게 제한은 항상 사람들의 몸무게 중 최댓값보다 크게 주어지므로 사람들을 구출할 수 없는 경우는 없습니다.

[입출력 예]<br>
people,	limit	|   return
- [70, 50, 80, 50],	100	  |  3
- [70, 80, 50], 	100  |	3


```python
#효율성 통과 못함
def solution(people,limit):
    people.sort(reverse=True)
    count = 0
    while people:
        count += 1
        first = people.pop(0)
        if people and first + people[-1] <= limit:
            people.pop(-1)

    return count
```


```python
solution([70,50,80,50],100)
```




    3




```python
solution([70,80,50],100)
```




    3




```python
#deque사용해서 효율성을 높임- pop()은 마지막, popleft()는 첫번째
from collections import deque
def solution(people,limit):
    de = deque(sorted(people,reverse=True))
    count = 0
    while de:
        count += 1
        first = de.popleft()
        if de and first + de[-1] <= limit:
            de.pop()

    return count
```


```python
#pop 말고 indexing만으로
def solution(people, limit) :
    answer = 0
    people.sort()

    a = 0
    b = len(people) - 1
    while a < b :
        if people[b] + people[a] <= limit :
            a += 1
            answer += 1
        b -= 1
    return len(people) - answer
```

# 5. 저울

하나의 양팔 저울을 이용하여 물건의 무게를 측정하려고 합니다. 이 저울의 양팔의 끝에는 물건이나 추를 올려놓는 접시가 달려 있고, 양팔의 길이는 같습니다. 또한, 저울의 한쪽에는 저울추들만 놓을 수 있고, 다른 쪽에는 무게를 측정하려는 물건만 올려놓을 수 있습니다.


저울추가 담긴 배열 weight가 매개변수로 주어질 때, 이 추들로 측정할 수 없는 양의 정수 무게 중 최솟값을 return 하도록 solution 함수를 작성해주세요.

예를 들어, 무게가 각각 [3, 1, 6, 2, 7, 30, 1]인 7개의 저울추를 주어졌을 때, 이 추들로 측정할 수 없는 양의 정수 무게 중 최솟값은 21입니다.

[제한 사항]

- 저울추의 개수는 1개 이상 10,000개 이하입니다.

- 각 추의 무게는 1 이상 1,000,000 이하입니다.


[입출력예]<br>
weight   |   return
- [3, 1, 6, 2, 7, 30, 1] | 21

[해결 방안]

가벼운 추부터 더했을 때, 다음 추의 무게가 이전 추 무게의 합+1보다 크면 측정할 수 없음.
다 중간에 측정할 수 없는 숫자가 없으면, 추 전체 무게 합+1이 답.


```python
def solution(weight):
    sum_ = 0
    weight.sort()
    for i in range(len(weight)-1):
        sum_ += weight[i]
        if sum_+1 < weight[i+1]:
            return sum_+1
    return sum(weight)+1
```


```python
solution([3,1,6,2,7,30,1])
```




    21



# 6. 섬 연결하기

문제 설명
n개의 섬 사이에 다리를 건설하는 비용(costs)이 주어질 때, 최소의 비용으로 모든 섬이 서로 통행 가능하도록 만들 때 필요한 최소 비용을 return 하도록 solution을 완성하세요.

다리를 여러 번 건너더라도, 도달할 수만 있으면 통행 가능하다고 봅니다. 예를 들어 A 섬과 B 섬 사이에 다리가 있고, B 섬과 C 섬 사이에 다리가 있으면 A 섬과 C 섬은 서로 통행 가능합니다.

제한사항

- 섬의 개수 n은 1 이상 100 이하입니다.
- costs의 길이는 ((n-1) * n) / 2이하입니다.
- 임의의 i에 대해, costs[i][0] 와 costs[i] [1]에는 다리가 연결되는 두 섬의 번호가 들어있고, costs[i] [2]에는 이 두 섬을 연결하는 다리를 건설할 때 드는 비용입니다.
- 같은 연결은 두 번 주어지지 않습니다. 또한 순서가 바뀌더라도 같은 연결로 봅니다. 즉 0과 1 사이를 연결하는 비용이 주어졌을 때, 1과 0의 비용이 주어지지 않습니다.
- 모든 섬 사이의 다리 건설 비용이 주어지지 않습니다. 이 경우, 두 섬 사이의 건설이 불가능한 것으로 봅니다.
- 연결할 수 없는 섬은 주어지지 않습니다.

[입출력 예]<br>
n,	costs  |	return
- 4,	[[0,1,1],[0,2,2],[1,2,5],[1,3,1],[2,3,8]]	|   4


```python
def solution(n,costs):
    costs.sort(key=lambda x:x[2])
    connected = set()  #연결된 섬들
    
    a,b,c = costs.pop(0) #첫번째로 비용이 낮은 다리 건설
    connected.add(a) 
    connected.add(b)
    
    answer = c
    
    while len(connected) != n: #섬이 전부 연결되지 않은 동안
        min_ = 999999999
        for f,t,w in costs: 
            if f in connected or t in connected: #연결할 수 있고
                if not (f in connected and t in connected) and w <min_: #이미 연결되어서 필요가 없지 않고 비용이 min_보다 작으면
                    min_ = w #min에 비용 update
                    f_,t_ = f,t #추가할 섬에 연결된 섬들 update
                    
        connected.add(f_) #연결된 섬들에 추가
        connected.add(t_)
        answer += min_ #총 비용에 최소 비용 추가
        
    return answer     
```


```python
solution(4, [[0,1,1],[0,2,2],[1,2,5],[1,3,1],[2,3,8]] )
```




    4



# 7. 단속 카메라

고속도로를 이동하는 모든 차량이 고속도로를 이용하면서 단속용 카메라를 한 번은 만나도록 카메라를 설치하려고 합니다.

고속도로를 이동하는 차량의 경로 routes가 매개변수로 주어질 때, 모든 차량이 한 번은 단속용 카메라를 만나도록 하려면 최소 몇 대의 카메라를 설치해야 하는지를 return 하도록 solution 함수를 완성하세요.

제한사항

- 차량의 대수는 1대 이상 10,000대 이하입니다.
- routes에는 차량의 이동 경로가 포함되어 있으며 routes[i][0]에는 i번째 차량이 고속도로에 진입한 지점, routes[i][1]에는 i번째 차량이 고속도로에서 나간 지점이 적혀 있습니다.
- 차량의 진입/진출 지점에 카메라가 설치되어 있어도 카메라를 만난것으로 간주합니다.
- 차량의 진입 지점, 진출 지점은 -30,000 이상 30,000 이하입니다.

[입출력 예]

routes  |	return
- [[-20,-15], [-14,-5], [-18,-13], [-5,-3]]	|  2

[해결 방안]

routes를 나간 순서대로 sort 후, 가장 최근 카메라 위치보다 나중에 들어왔으면 출구에 카메라 설치


```python
def solution(routes):
    routes.sort(key = lambda x:(x[1],x[0]))
    count = 0
    cam = -999999
    
    for i in routes:
        if cam < i[0]:
            count += 1
            cam = i[1]
    return count
```


```python
solution([[-20,-15], [-14,-5], [-18,-13], [-5,-3]])
```




    2



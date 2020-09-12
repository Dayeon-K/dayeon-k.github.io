---
title: "Programmers-Heap"
date: 2020-08-16 08:26:28 -0400
classes: wide
toc: true
categories: Algorithm
---

# Heap

https://www.daleseo.com/python-heapq/ 참고<br>
min heap을 사용하면 원소들이 항상 정렬된 상태로 추가되고 삭제되며, min heap에서 가장 작은값은 언제나 인덱스 0, 즉, 이진 트리의 루트에 위치함. 내부적으로 min heap 내의 모든 원소(k)는 항상 자식 원소들(2k+1, 2k+2) 보다 크기가 작거나 같도록 원소가 추가되고 삭제됨.<br>
heapq 는 O(logN)의 복잡도로 push 가능 -> 효율성 좋아짐.

- import heapq as hq
- heap = []
- hq.heapify(heap)
- hq.heappop(heap) #min
- hq.heappush(heap,n)</ul>
#최대 힙은 -n 또는 (-n,n)으로 넣음

# 1. 더맵게

매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.

섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)<br>
Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.<br>
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

제한 사항
- scoville의 길이는 2 이상 1,000,000 이하입니다.
- K는 0 이상 1,000,000,000 이하입니다.
- scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
- 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

[입출력 예]
scoville, K	|  return
- [1, 2, 3, 9, 10, 12],	7	|  2


```python
import heapq as hq
def solution(scoville,K):
    hq.heapify(scoville)
    count = 0
    while scoville[0] < K and len(scoville)>=2:  
         hq.heappush(scoville,hq.heappop(scoville)+hq.heappop(scoville)*2)
        count += 1  
        if len(scoville)==1 and scoville[0]< K: #다 합쳐도 K가 안되는 경우
            return -1
    return count
```


```python
import heapq as hq

def solution(scoville, K):

    hq.heapify(scoville)
    answer = 0
    while True:
        first = hq.heappop(scoville)
        if first >= K: #안섞어도 되는 경우
            break
        if len(scoville) == 0: #다 섞었는데 K가 안되는 경우
            return -1
        second = hq.heappop(scoville)
        hq.heappush(scoville, first + second*2)
        answer += 1  

    return answer

```




    2



# 2. 라면공장

라면 공장에서는 하루에 밀가루를 1톤씩 사용합니다. 원래 밀가루를 공급받던 공장의 고장으로 앞으로 k일 이후에야 밀가루를 공급받을 수 있기 때문에 해외 공장에서 밀가루를 수입해야 합니다.

해외 공장에서는 향후 밀가루를 공급할 수 있는 날짜와 수량을 알려주었고, 라면 공장에서는 운송비를 줄이기 위해 최소한의 횟수로 밀가루를 공급받고 싶습니다.

현재 공장에 남아있는 밀가루 수량 stock, 밀가루 공급 일정(dates)과 해당 시점에 공급 가능한 밀가루 수량(supplies), 원래 공장으로부터 공급받을 수 있는 시점 k가 주어질 때, 밀가루가 떨어지지 않고 공장을 운영하기 위해서 최소한 몇 번 해외 공장으로부터 밀가루를 공급받아야 하는지를 return 하도록 solution 함수를 완성하세요.

dates[i]에는 i번째 공급 가능일이 들어있으며, supplies[i]에는 dates[i] 날짜에 공급 가능한 밀가루 수량이 들어 있습니다.

[입출력 예]
stock, dates, supplies, k   |  result
- 4, [4,10,15], [20,5,10], 30  |  2


```python
import heapq as hq

def solution(stock, dates, supplies, k):
    answer = 0
    heap =[]
    
    while stock < k:
        while True:
            if len(dates)==0 or dates[0] > stock: #재고가 없으면 break
                break
            # 아니면
            d = dates.pop(0)  #dates삭제
            s = supplies.pop(0) # 재고 여유가 있는 동안 가능한 공급액을 heap에 추가
            hq.heappush(heap,-s) 
            
        stock += hq.heappop(heap) * (-1) # 가능한 것 중 가장 큰 공급액을 공급 받음
        answer += 1 #횟수 추가
        
    return answer            
```


```python
solution(stock =4, dates =[1,2,3,4],supplies=[10,20,30,40],k=100)
#solution(stock=4,dates=[4,10,15],[20,5,10])
```




    4



# 3. 디스크 컨트롤러

하드디스크는 한 번에 하나의 작업만 수행할 수 있습니다. 디스크 컨트롤러를 구현하는 방법은 여러 가지가 있습니다. 가장 일반적인 방법은 요청이 들어온 순서대로 처리하는 것입니다.

예를들어

- 0ms 시점에 3ms가 소요되는 A작업 요청
- 1ms 시점에 9ms가 소요되는 B작업 요청
- 2ms 시점에 6ms가 소요되는 C작업 요청

![/images/programmers/11.PNG]

- A: 3ms 시점에 작업 완료 (요청에서 종료까지 : 3ms)
- B: 1ms부터 대기하다가, 3ms 시점에 작업을 시작해서 12ms 시점에 작업 완료(요청에서 종료까지 : 11ms)
- C: 2ms부터 대기하다가, 12ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 16ms)

이 때 각 작업의 요청부터 종료까지 걸린 시간의 평균은 10ms(= (3 + 11 + 16) / 3)가 됩니다.

하지만 A → C → B 순서대로 처리하면

![/images/programmers/12.PNG]

- A: 3ms 시점에 작업 완료(요청에서 종료까지 : 3ms)
- C: 2ms부터 대기하다가, 3ms 시점에 작업을 시작해서 9ms 시점에 작업 완료(요청에서 종료까지 : 7ms)
- B: 1ms부터 대기하다가, 9ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 17ms)

이렇게 A → C → B의 순서로 처리하면 각 작업의 요청부터 종료까지 걸린 시간의 평균은 9ms(= (3 + 7 + 17) / 3)가 됩니다.

각 작업에 대해 [작업이 요청되는 시점, 작업의 소요시간]을 담은 2차원 배열 jobs가 매개변수로 주어질 때, 작업의 요청부터 종료까지 걸린 시간의 평균을 가장 줄이는 방법으로 처리하면 평균이 얼마가 되는지 return 하도록 solution 함수를 작성해주세요. (단, 소수점 이하의 수는 버립니다)

제한 사항
- jobs의 길이는 1 이상 500 이하입니다.
- jobs의 각 행은 하나의 작업에 대한 [작업이 요청되는 시점, 작업의 소요시간] 입니다.
- 각 작업에 대해 작업이 요청되는 시간은 0 이상 1,000 이하입니다.
- 각 작업에 대해 작업의 소요시간은 1 이상 1,000 이하입니다.
- 하드디스크가 작업을 수행하고 있지 않을 때에는 먼저 요청이 들어온 작업부터 처리합니다.

[입출력 예]
jobs	|   return
- [[0, 3], [1, 9], [2, 6]]	|  9


```python
import heapq as hq

def solution(jobs):
    jobs.sort(key =lambda x:(x[0],x[1]))
    time = 0 #현재 시간
    heap =[]
    start, end = 0, 0
    n = len(jobs)
    count = 0 #몇개 실행했는지

    while count < n: #전부 실행하기 전까지
        
        while jobs and jobs[0][0] <= time: #실행해야되는거 heap에 넣음
            hq.heappush(heap,(jobs[0][1]))
            start += jobs[0][0]
            jobs.pop(0)
        
        if not heap and jobs:  # 당장 실행할건 없지만 다 실행하지는 않은 경우
            start += jobs[0][0]
            time = jobs[0][0] + jobs[0][1]
            end += time
            jobs.pop(0)
            count += 1
        
        else: #당장 실행해야될게 있는 경우
            d = hq.heappop(heap)
            time += d
            end += time
            count += 1

    return int((end-start)/n)
```


```python
solution([[0, 3], [1, 9], [2, 6]] )
```




    9



# 4. 쇠막대기

![/images/programmers/13.png]
```python
def solution(arrangement):
    arrange = arrangement.replace('()','|')
    count = 0
    answer = 0
    
    for i in arrange:
        if i == '(': 
            count += 1   #현재 처리하는 막대의 개수
            answer += 1  #가진 막대수
        elif i == '|':
            answer += count #레이저 지날때마다 처리하는 막대수만큼 생김
        else:
            count -= 1 #처리하는 막대수 줄어들음
    return answer  
```

# 5. 이중우선순위큐

이중 우선순위 큐는 다음 연산을 할 수 있는 자료구조를 말합니다.

명령어	  |  수신 탑(높이)
- I 숫자	큐에 주어진 숫자를 삽입합니다.
- D 1	큐에서 최댓값을 삭제합니다.
- D -1	큐에서 최솟값을 삭제합니다.


이중 우선순위 큐가 할 연산 operations가 매개변수로 주어질 때, 모든 연산을 처리한 후 큐가 비어있으면 [0,0] 비어있지 않으면 [최댓값, 최솟값]을 return 하도록 solution 함수를 구현해주세요.

[제한사항]
- operations는 길이가 1 이상 1,000,000 이하인 문자열 배열입니다.
- operations의 원소는 큐가 수행할 연산을 나타냅니다.
- 원소는 “명령어 데이터” 형식으로 주어집니다.
- 최댓값/최솟값을 삭제하는 연산에서 최댓값/최솟값이 둘 이상인 경우, 하나만 삭제합니다.
- 빈 큐에 데이터를 삭제하라는 연산이 주어질 경우, 해당 연산은 무시합니다.

[입출력예] <br>
operations	|  return
- ["I 16","D 1"]	|   [0,0]
- ["I 7","I 5","I -5","D -1"]	|   [7,5]


```python
import heapq as hq

def solution(operations):
    heap =[]
    for op in operations:
        if "I" in op:
            hq.heappush(heap,int(op[2:]))
        elif "D -" in op and heap:
            hq.heappop(heap)
        elif heap:
            heap.remove(max(heap))
        print(op,heap)
    if not heap:
        return [0,0]
    else:
        return [max(heap),hq.heappop(heap)]
```


```python
solution(['I 7','I 5','I -5','D -1'])
```




    [7, -5]




```python
solution(['I 16','D 1'])
```




    [0, 0]




```python
solution(["I -45", "I 653", "D 1", "I -642", "I 45", "I 97", "D 1", "D -1", "I 333"])
```




    [333, -642]




```python
solution(['I 1', 'I 2', 'I 3', 'I 4', 'I 5', 'I 6', 'I 7', 'I 8', 'I 9', 'I 10', 'D 1', 'D -1', 'D 1', 'D -1', 'I 1', 'I 2', 'I 3', 'I 4', 'I 5', 'I 6', 'I 7', 'I 8', 'I 9', 'I 10', 'D 1', 'D -1', 'D 1', 'D -1'])
```

    I 1 [1]
    I 2 [1, 2]
    I 3 [1, 2, 3]
    I 4 [1, 2, 3, 4]
    I 5 [1, 2, 3, 4, 5]
    I 6 [1, 2, 3, 4, 5, 6]
    I 7 [1, 2, 3, 4, 5, 6, 7]
    I 8 [1, 2, 3, 4, 5, 6, 7, 8]
    I 9 [1, 2, 3, 4, 5, 6, 7, 8, 9]
    I 10 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    D 1 [1, 2, 3, 4, 5, 6, 7, 8, 9]
    D -1 [2, 4, 3, 8, 5, 6, 7, 9]
    D 1 [2, 4, 3, 8, 5, 6, 7]
    D -1 [3, 4, 6, 8, 5, 7]
    I 1 [1, 4, 3, 8, 5, 7, 6]
    I 2 [1, 2, 3, 4, 5, 7, 6, 8]
    I 3 [1, 2, 3, 3, 5, 7, 6, 8, 4]
    I 4 [1, 2, 3, 3, 4, 7, 6, 8, 4, 5]
    I 5 [1, 2, 3, 3, 4, 7, 6, 8, 4, 5, 5]
    I 6 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7]
    I 7 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7, 7]
    I 8 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7, 7, 8]
    I 9 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7, 7, 8, 9]
    I 10 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7, 7, 8, 9, 10]
    D 1 [1, 2, 3, 3, 4, 6, 6, 8, 4, 5, 5, 7, 7, 8, 9]
    D -1 [2, 3, 3, 4, 4, 6, 6, 8, 9, 5, 5, 7, 7, 8]
    D 1 [2, 3, 3, 4, 4, 6, 6, 8, 5, 5, 7, 7, 8]
    D -1 [3, 3, 6, 4, 4, 6, 8, 8, 5, 5, 7, 7]





    [8, 3]



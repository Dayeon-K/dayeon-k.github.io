---
title: "Programmers-이분탐색"
date: 2020-08-01 08:26:28 -0400
classes: wide
toc: true
categories: [Algorithm]
---

# 이분 탐색(이진 탐색)

sort된 자료에서
1. 중간 위치를 찾는다
2. 찾는 값과 중간 위치 값을 비교한다
3. 같으면 위치 번호를 결과값으로 돌려줌
4. 찾는 값이 중간 위치 값보다 크면 중간 위치의 오른쪽만을 탐색(1부터 반복)
5. 찾는 값이 중간 위치 값보자 작으면 중간 위치의 왼쪽만을 탐색(1부터 반복)

출처: 모두의 알고리즘 (이분 탐색)

이분 탐색의 계산 복잡도는 O(logN)으로, 순차 탐색보다 효율적


```python
#예제 코드
def binary_search(a,x): #a가 리스트, x가 찾는값
    start = 0
    end = len(a) -1
    
    while start <= end:
        mid = (start + end)//2
        
        if x==a[mid]:
            return min
        elif x > a[mid]:
            start = mid +1
        else:
            end = mid -1
    return -1 #찾지 못했을 때
```

# 1. 입국 심사

[문제 설명]

n명이 입국심사를 위해 줄을 서서 기다리고 있습니다. 각 입국심사대에 있는 심사관마다 심사하는데 걸리는 시간은 다릅니다.

처음에 모든 심사대는 비어있습니다. 한 심사대에서는 동시에 한 명만 심사를 할 수 있습니다. 가장 앞에 서 있는 사람은 비어 있는 심사대로 가서 심사를 받을 수 있습니다. 하지만 더 빨리 끝나는 심사대가 있으면 기다렸다가 그곳으로 가서 심사를 받을 수도 있습니다.

모든 사람이 심사를 받는데 걸리는 시간을 최소로 하고 싶습니다.

입국심사를 기다리는 사람 수 n, 각 심사관이 한 명을 심사하는데 걸리는 시간이 담긴 배열 times가 매개변수로 주어질 때, 모든 사람이 심사를 받는데 걸리는 시간의 최솟값을 return 하도록 solution 함수를 작성해주세요.

[제한사항]

- 입국심사를 기다리는 사람은 1명 이상 1,000,000,000명 이하입니다.
- 각 심사관이 한 명을 심사하는데 걸리는 시간은 1분 이상 1,000,000,000분 이하입니다.
- 심사관은 1명 이상 100,000명 이하입니다.

[입출력 예]

n	|  times	|  return
- 6	|  [7, 10]	|  28


```python
def solution(n, times):
    start = 1
    end = n * times[0]
    answer = end

    while start <= end:
        mid = (start+end)//2
        
        count = 0
        for i in times:
            count += mid//i
            
        if count >= n:
            if answer > mid:
                answer = mid;
            end = mid-1
        else:
            start = mid +1

    return answer
```


```python
solution(6,[7,10])
```




    28




```python
def solution(n, times):
    answer = 0
    min_ =0
    max_ = times[0]*n
    while True:
        mid = int((min_+max_)/2)
        count = 0
        for i in times:
            count+= int(mid/i)

        if count >= n:
            max_ = mid
        else: 
            min_ = mid +1

        if min_ == max_:
            return min_
    return answer
```

# 2. 징검다리

[문제 설명]

출발지점부터 distance만큼 떨어진 곳에 도착지점이 있습니다. 그리고 그사이에는 바위들이 놓여있습니다. 바위 중 몇 개를 제거하려고 합니다.
예를 들어, 도착지점이 25만큼 떨어져 있고, 바위가 [2, 14, 11, 21, 17] 지점에 놓여있을 때 바위 2개를 제거하면 출발지점, 도착지점, 바위 간의 거리가 아래와 같습니다.

제거한 바위의 위치	|  각 바위 사이의 거리	|  거리의 최솟값
- [21, 17]	|  [2, 9, 3, 11]	|  2
- [2, 21]	|  [11, 3, 3, 8]	|  3
- [2, 11]	|  [14, 3, 4, 4]	|  3
- [11, 21]	| [2, 12, 3, 8]	 |  2
- [2, 14]	| [11, 6, 4, 4]	|  4

위에서 구한 거리의 최솟값 중에 가장 큰 값은 4입니다.

출발지점부터 도착지점까지의 거리 distance, 바위들이 있는 위치를 담은 배열 rocks, 제거할 바위의 수 n이 매개변수로 주어질 때, 바위를 n개 제거한 뒤 각 지점 사이의 거리의 최솟값 중에 가장 큰 값을 return 하도록 solution 함수를 작성해주세요.

[제한사항]

- 도착지점까지의 거리 distance는 1 이상 1,000,000,000 이하입니다.
- 바위는 1개 이상 50,000개 이하가 있습니다.
- n 은 1 이상 바위의 개수 이하입니다.

[입출력 예]

distance	|  rocks	|  n	|  return
- 25	|  [2, 14, 11, 21, 17]	|  2	|  4

[문제 해결]

포인터 기준은 돌 사이의 (최소)거리. 앞에서부터 돌 간 거리를 탐색하다가 돌 간 거리가 기준(포인터)보다 크면 없애야 하는 돌 +1 한다. 없애야하는 돌의 수가 n을 넘어가면 바위 제거를 줄여야하므로 포인터인 돌 사이의 최소거리 기준을 줄여야한다.
https://m.post.naver.com/viewer/postView.nhn?volumeNo=27217004&memberNo=33264526


```python
def solution(distance,rocks,n):
    rocks.sort()
    rocks.append(distance)
    left,right = 0, distance
    
    answer =0
    while left <= right:
        prev = 0 #이전 돌
        mins = 1000000000000000000
        removed_rocks = 0
        
        mid = (left + right)//2
        
        for i in range(len(rocks)):
            if rocks[i]-prev < mid:
                removed_rocks +=1
            else:
                mins = min(mins,rocks[i]-prev)
                prev = rocks[i]
                
        if removed_rocks >n:
            right = mid-1
        else:
            answer = mins
            left = mid +1
    return answer
```


```python
solution(25, [2, 14, 11, 21, 17], 2 )
```




    4



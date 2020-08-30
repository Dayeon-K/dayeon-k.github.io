---
title: "Programmers-Stack,Queue"
date: 2020-07-11 08:26:28 -0400
classes: wide
toc: true
categories: Algorithm
---

# Stack, Queue

Stack : LIFO, Queue: FIFO

collections.deque : O(1) 효율성으로 append, pop 가능
- de.appendleft()
- de.append()
- de.pop()
- de.popleft()

## 주식 가격

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요. <br>

제한사항
- prices의 각 가격은 1 이상 10,000 이하인 자연수입니다.
- prices의 길이는 2 이상 100,000 이하입니다.

prices	|   return
- [1, 2, 3, 2, 3] |	[4, 3, 1, 1, 0]

입출력 예 설명
- 1초 시점의 ₩1은 끝까지 가격이 떨어지지 않았습니다.
- 2초 시점의 ₩2은 끝까지 가격이 떨어지지 않았습니다.
- 3초 시점의 ₩3은 1초뒤에 가격이 떨어집니다. 따라서 1초간 가격이 떨어지지 않은 것으로 봅니다.
- 4초 시점의 ₩2은 1초간 가격이 떨어지지 않았습니다.
- 5초 시점의 ₩3은 0초간 가격이 떨어지지 않았습니다.


```python
def solution(prices):
    answer = []
    n = len(prices)
    for i in range(n-1):
        flag = 0
        for j in range(i+1,n):
            flag = j   #flag에 j를 계속 저장
            if prices[j]<prices[i]: #가격이 떨어지면 for문 break
                break
        answer.append(flag-i)  #break한 위치에서 비교 위치를 빼줌
    print(answer+[0]) #답 + 마지막은 항상 0
```


```python
solution([1,2,3,2,3])
```

    [4, 3, 1, 1, 0]
    

## 기능 개발

프로그래머스 팀에서는 기능 개선 작업을 수행 중입니다. 각 기능은 진도가 100%일 때 서비스에 반영할 수 있습니다.

또, 각 기능의 개발속도는 모두 다르기 때문에 뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.

먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때 각 배포마다 몇 개의 기능이 배포되는지를 return 하도록 solution 함수를 완성하세요.

제한 사항
- 작업의 개수(progresses, speeds배열의 길이)는 100개 이하입니다.
- 작업 진도는 100 미만의 자연수입니다.
- 작업 속도는 100 이하의 자연수입니다.
- 배포는 하루에 한 번만 할 수 있으며, 하루의 끝에 이루어진다고 가정합니다. 예를 들어 진도율이 95%인 작업의 개발 속도가 하루에 4%라면 배포는 2일 뒤에 이루어집니다.

progresses,	speeds	|  return
- [93,30,55],[1,30,5]	|  [2,1]

입출력 예 설명
첫 번째 기능은 93% 완료되어 있고 하루에 1%씩 작업이 가능하므로 7일간 작업 후 배포가 가능합니다.
두 번째 기능은 30%가 완료되어 있고 하루에 30%씩 작업이 가능하므로 3일간 작업 후 배포가 가능합니다. 하지만 이전 첫 번째 기능이 아직 완성된 상태가 아니기 때문에 첫 번째 기능이 배포되는 7일째 배포됩니다.
세 번째 기능은 55%가 완료되어 있고 하루에 5%씩 작업이 가능하므로 9일간 작업 후 배포가 가능합니다.

따라서 7일째에 2개의 기능, 9일째에 1개의 기능이 배포됩니다.


```python
from math import ceil #올림 함수
def solution(progresses,speeds):
    answer = []
    time = [ceil((100-x)/y) for x,y in zip(progresses,speeds)] #배포까지 걸리는 기간
    while time:
        count = 1
        time_zero = time.pop(0)
        while time and time_zero >=time[0]: #time이 남아있는 동안 뒤에가 더 크면 count에 1 추가
            count +=1
            time.pop(0)
        answer.append(count)
    return answer
```


```python
solution([93,30,55],[1,30,5])
```




    [2, 1]



## 다리를 지나는 트럭

트럭 여러 대가 강을 가로지르는 일 차선 다리를 정해진 순으로 건너려 합니다. 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 알아내야 합니다. 트럭은 1초에 1만큼 움직이며, 다리 길이는 bridge_length이고 다리는 무게 weight까지 견딥니다.
※ 트럭이 다리에 완전히 오르지 않은 경우, 이 트럭의 무게는 고려하지 않습니다.

예를 들어, 길이가 2이고 10kg 무게를 견디는 다리가 있습니다. 무게가 [7, 4, 5, 6]kg인 트럭이 순서대로 최단 시간 안에 다리를 건너려면 다음과 같이 건너야 합니다.

경과 시간	|  다리를 지난 트럭	|  다리를 건너는 트럭	|  대기 트럭
- 0	 |  []	|  []	|  [7,4,5,6]
- 1~2	|  []	|  [7]	|  [4,5,6]
- 3	 | [7]	|  [4]	|  [5,6]
- 4	|  [7]	|  [4,5]	|  [6]
- 5	|  [7,4]	|  [5]	|  [6]
- 6~7	|  [7,4,5]	|  [6]	|  []
- 8	 |  [7,4,5,6]	|  []	|  []</ul>
따라서, 모든 트럭이 다리를 지나려면 최소 8초가 걸립니다.

solution 함수의 매개변수로 다리 길이 bridge_length, 다리가 견딜 수 있는 무게 weight, 트럭별 무게 truck_weights가 주어집니다. 이때 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 return 하도록 solution 함수를 완성하세요.

제한 조건
- bridge_length는 1 이상 10,000 이하입니다.
- weight는 1 이상 10,000 이하입니다.
- truck_weights의 길이는 1 이상 10,000 이하입니다.
- 모든 트럭의 무게는 1 이상 weight 이하입니다.

bridge_length, weight, truck_weights	|  return
- 2, 10, [7,4,5,6]  |	8
- 100, 100, [10]  |	101
- 100, 100, [10,10,10,10,10,10,10,10,10,10]	|  110

다리에 있는 트럭의 무게 합을 sum() 함수를 쓰면 시간 초과가 뜨기 때문에, 무게를 저장하는 객체를 생성해야함


```python
def solution(bridge_length, weight, truck_weights):
    answer = 0
    bridge = [0] * bridge_length   # 다리 위에 있는 트럭
    on_bridge = 0  #다리 위 트럭의 무게
    while truck_weights:
        on_bridge -= bridge.pop(0)   # 시행 때 마다 다리 끝에 있는 애의 무게를 빼줌
        answer += 1  #시행 때 마다 시간 +1
        if on_bridge + truck_weights[0] > weight:  # 현재 다리 위 무게 + 새로 올라갈 트럭 무게가 기준보다 높다면
            bridge.append(0)   #0 을 붙임 (트럭을 보내지 않음)
        else:
            tmp  = truck_weights.pop(0)  #트럭 배열 중 앞에 것
            on_bridge += tmp   #무게 합에 추가
            bridge.append(tmp)  #다리에 추가
    return answer + bridge_length   # 전체 시간 + 마지막 트럭이 다리를 건너는 시간
```

## 프린터

일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.

1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.


예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

제한사항
- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

priorities	|  location	|  return
- [2, 1, 3, 2] | 2	|  1
- [1, 1, 9, 1, 1, 1] |  0	|  5


```python
def solution(priorities,location):
    loc = list(range(len(priorities)))
    count = 0
    while True:
        tmp = priorities.pop(0)
        loc_tmp = loc.pop(0)

        if any(tmp < p for p in priorities):
            priorities.append(tmp)
            loc.append(loc_tmp)
        elif loc_tmp==location:
            count += 1
            return count
        else: count += 1
#deque 사용하면 효율성 향상!!!!
```

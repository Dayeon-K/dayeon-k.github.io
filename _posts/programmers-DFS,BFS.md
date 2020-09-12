# DFS/BFS

- DFS: 깊이 우선 탐색. 탐색을 트리 가장 아래까지 진행
- BFS: 너비 우선 탐색. 탐색을 같은 계층 단위로 진행 후 다음 계층으로 

# 1. 타겟 넘버

[문제 설명]

n개의 음이 아닌 정수가 있습니다. 이 수를 적절히 더하거나 빼서 타겟 넘버를 만들려고 합니다. 예를 들어 [1, 1, 1, 1, 1]로 숫자 3을 만들려면 다음 다섯 방법을 쓸 수 있습니다.

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3


사용할 수 있는 숫자가 담긴 배열 numbers, 타겟 넘버 target이 매개변수로 주어질 때 숫자를 적절히 더하고 빼서 타겟 넘버를 만드는 방법의 수를 return 하도록 solution 함수를 작성해주세요.

[제한사항] 

- 주어지는 숫자의 개수는 2개 이상 20개 이하입니다.
- 각 숫자는 1 이상 50 이하인 자연수입니다.
- 타겟 넘버는 1 이상 1000 이하인 자연수입니다.

[입출력 예]

numbers	|  target	|  return
- [1, 1, 1, 1, 1]	|  3	|  5


```python
from itertools import combinations
def solution(numbers,target):
    count =0
    n = len(numbers)
    for mp in range(n-1):
        comb = combinations(list(range(n)),mp) #-붙을 숫자의 인덱스 combination 만들기
        for c in comb: #combination 탐색
            tmp = 0 
            for ind in range(n): 
                if ind in c: #-붙는 인덱스면
                    tmp -= numbers[ind] #빼기
                else: #아니면
                    tmp += numbers[ind] #더하기
            if tmp == target: #결과가 타겟이랑 같으면
                count += 1 #count에 1 추가
    return count
```


```python
solution([1,1,1,1,1,],3)
```




    5




```python
#재귀를 이용한 풀이
def solution(numbers, target):
    if not numbers and target == 0 : #numbers가 끝났을때 target이 지워졌으면 1
        return 1
    elif not numbers:
        return 0
    else:
        return solution(numbers[1:], target-numbers[0]) + solution(numbers[1:], target+numbers[0]) #1. target에 제일 앞의 값을 뺴거나 더하는 두 가지 경우를 합, tree처럼 모든 number에 대해 경우가 나눠짐
```

# 2. 네트워크

[문제 설명]

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.


컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

[제한사항]

- 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
- 각 컴퓨터는 0부터 n-1인 정수로 표현합니다.
- i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
- computer[i][i]는 항상 1입니다.

[입출력 예]

n	|  computers	|  return
- 3	|  [[1, 1, 0], [1, 1, 0], [0, 0, 1]]	|  2
- 3	|  [[1, 1, 0], [1, 1, 1], [0, 1, 1]]	|  1


```python
def solution(n, computers):
    networks = []
    tmp = list(range(n)) #연결해야할 모든 컴퓨터

    while tmp: #모든 컴퓨터를 확인하는 동안
        check = set([ind for ind,c in enumerate(computers[tmp[0]]) if c==1]) #확인해야할 컴퓨터
        con =set([ind for ind,c in enumerate(computers[tmp[0]]) if c==1]) #연결된 컴퓨터
        
        while check: #확인해야할 게 남아있는 동안
            check_tmp = set()
            
            for i in check:
                for j in range(n):
                    if computers[i][j] == 1 and j not in con: #con에 없고 연결되어있다면
                        check_tmp.add(j) #check_tmp에 추가
            check = check_tmp #check update
            con.update(check) #con에 check 추가

        for i in con: #확인해야할 목록에서 삭제
            if i in tmp:
                tmp.remove(i)
            
        networks.append(list(con)) #연결된 모든 컴퓨터 네트워크에 추가
        
    return len(networks) #네트워크 개수
```


```python
solution(3, [[1, 1, 0], [1, 1, 0], [0, 0, 1]])
```




    2



# 3. 단어 변환

[문제 설명]

두 개의 단어 begin, target과 단어의 집합 words가 있습니다. 아래와 같은 규칙을 이용하여 begin에서 target으로 변환하는 가장 짧은 변환 과정을 찾으려고 합니다.

1. 한 번에 한 개의 알파벳만 바꿀 수 있습니다.
2. words에 있는 단어로만 변환할 수 있습니다.


예를 들어 begin이 hit, target가 cog, words가 [hot,dot,dog,lot,log,cog]라면 hit -> hot -> dot -> dog -> cog와 같이 4단계를 거쳐 변환할 수 있습니다.

두 개의 단어 begin, target과 단어의 집합 words가 매개변수로 주어질 때, 최소 몇 단계의 과정을 거쳐 begin을 target으로 변환할 수 있는지 return 하도록 solution 함수를 작성해주세요.

[제한사항]
- 각 단어는 알파벳 소문자로만 이루어져 있습니다.
- 각 단어의 길이는 3 이상 10 이하이며 모든 단어의 길이는 같습니다.
- words에는 3개 이상 50개 이하의 단어가 있으며 중복되는 단어는 없습니다.
- begin과 target은 같지 않습니다.
- 변환할 수 없는 경우에는 0를 return 합니다.

[입출력 예]

begin	|  target	|  words	|  return
- "hit"	|  "cog"	|  ["hot", "dot", "dog", "lot", "log", "cog"]	|  4
- "hit"	|  "cog"	|  ["hot", "dot", "dog", "lot", "log"]	|  0


```python
def letters(str1,str2): #다른 알파벳이 하나이면 True, 아니면 False를 return하는 함수 만들기
    count = 0
    for i,k in zip(str1,str2):
        if i != k:
            count += 1
    if count == 1:
        return True
    else:
        return False

def solution(begin,target,words):
    change = [[begin]] #단계별로 변화할 수 있는 세트를 저장함
    words_tmp = words.copy() #이미 추가한 단어를 삭제하기 위해 만드는 리스트
    while words_tmp:
        tmp = set() #다음 단계에서 변화 가능한 단어릉 저장하는 세트
        for l in change[-1]: #마지막 변환 단계에 있는 단어들
            for word in words:#바꿀 수 있는 단어들
                if letters(l,word) and word in words_tmp:#알파벳 하나가 다르고 변환되지 않은 단어라면
                    tmp.add(word) #tmp에 추가
                    words_tmp.remove(word) #추가했으면 words_tmp에서 제거
        if not tmp: #tmp가 비어있으면 더 이상 변환할 수 있는 단어가 없다는 뜻
            break
        else:
            change.append(tmp) #change에 변화 가능한 세트를 추가
        print(change)
        if target in tmp: #target 단어가 변화 가능한 목록에 있으면
            return len(change)-1 #변화 횟수를 출력
    return 0 #tmp가 비었거나 words_tmp를 다썼는데 target 만드는데 실패
```


```python
solution("hit", "cog", ["hot", "dot", "dog", "lot", "log", "cog"])
```

    [['hit'], {'hot'}]
    [['hit'], {'hot'}, {'dot', 'lot'}]
    [['hit'], {'hot'}, {'dot', 'lot'}, {'log', 'dog'}]
    [['hit'], {'hot'}, {'dot', 'lot'}, {'log', 'dog'}, {'cog'}]





    4




```python
solution("hit", "cog",["hot", "dot", "dog", "lot", "log"] )
```

    [['hit'], {'hot'}]
    [['hit'], {'hot'}, {'dot', 'lot'}]
    [['hit'], {'hot'}, {'dot', 'lot'}, {'log', 'dog'}]





    0



# 4. 여행 경로

[문제 설명] 

주어진 항공권을 모두 이용하여 여행경로를 짜려고 합니다. 항상 ICN 공항에서 출발합니다.

항공권 정보가 담긴 2차원 배열 tickets가 매개변수로 주어질 때, 방문하는 공항 경로를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

[제한사항]

- 모든 공항은 알파벳 대문자 3글자로 이루어집니다.
- 주어진 공항 수는 3개 이상 10,000개 이하입니다.
- tickets의 각 행 [a, b]는 a 공항에서 b 공항으로 가는 항공권이 있다는 의미입니다.
- 주어진 항공권은 모두 사용해야 합니다.
- 만일 가능한 경로가 2개 이상일 경우 알파벳 순서가 앞서는 경로를 return 합니다.
- 모든 도시를 방문할 수 없는 경우는 주어지지 않습니다.

[입출력 예]

tickets	|  return
- [["ICN", "JFK"], ["HND", "IAD"], ["JFK", "HND"]]	|  ["ICN", "JFK", "HND", "IAD"]
- [["ICN", "SFO"], ["ICN", "ATL"], ["SFO", "ATL"], ["ATL", "ICN"], ["ATL","SFO"]]	|  ["ICN", "ATL", "ICN", "SFO", "ATL", "SFO"]


```python
from collections import deque

def solution(tickets):
    answer = []
    route = {}
    for i in tickets :
        route[i[0]] = route.get(i[0], []) + [i[1]]

    for i in route :
        route[i].sort(reverse = True)
    print(route)
    
    start = deque(['ICN'])
    path = []
    
    while start :
        print('start',start)
        top = start[-1]
        print(top)
        if top not in route or len(route[top]) == 0 :
            
            path.append(start.pop())
        
        else :
            start.append(route[top][-1])
            route[top].pop()
        print('path',path)
        
    path.reverse()
    return path
```


```python
solution([["ICN", "JFK"], ["HND", "IAD"], ["JFK", "HND"]] )
```

    {'ICN': ['JFK'], 'HND': ['IAD'], 'JFK': ['HND']}
    start deque(['ICN'])
    ICN
    path []
    start deque(['ICN', 'JFK'])
    JFK
    path []
    start deque(['ICN', 'JFK', 'HND'])
    HND
    path []
    start deque(['ICN', 'JFK', 'HND', 'IAD'])
    IAD
    path ['IAD']
    start deque(['ICN', 'JFK', 'HND'])
    HND
    path ['IAD', 'HND']
    start deque(['ICN', 'JFK'])
    JFK
    path ['IAD', 'HND', 'JFK']
    start deque(['ICN'])
    ICN
    path ['IAD', 'HND', 'JFK', 'ICN']





    ['ICN', 'JFK', 'HND', 'IAD']




```python
solution([["ICN", "SFO"], ["ICN", "ATL"], ["SFO", "ATL"], ["ATL", "ICN"], ["ATL","SFO"]])
```

    {'ICN': ['SFO', 'ATL'], 'SFO': ['ATL'], 'ATL': ['SFO', 'ICN']}
    start deque(['ICN'])
    ICN
    path []
    start deque(['ICN', 'ATL'])
    ATL
    path []
    start deque(['ICN', 'ATL', 'ICN'])
    ICN
    path []
    start deque(['ICN', 'ATL', 'ICN', 'SFO'])
    SFO
    path []
    start deque(['ICN', 'ATL', 'ICN', 'SFO', 'ATL'])
    ATL
    path []
    start deque(['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO'])
    SFO
    path ['SFO']
    start deque(['ICN', 'ATL', 'ICN', 'SFO', 'ATL'])
    ATL
    path ['SFO', 'ATL']
    start deque(['ICN', 'ATL', 'ICN', 'SFO'])
    SFO
    path ['SFO', 'ATL', 'SFO']
    start deque(['ICN', 'ATL', 'ICN'])
    ICN
    path ['SFO', 'ATL', 'SFO', 'ICN']
    start deque(['ICN', 'ATL'])
    ATL
    path ['SFO', 'ATL', 'SFO', 'ICN', 'ATL']
    start deque(['ICN'])
    ICN
    path ['SFO', 'ATL', 'SFO', 'ICN', 'ATL', 'ICN']





    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']




```python
from collections import defaultdict 

def dfs(graph, N, key, footprint):
    print(footprint)

    if len(footprint) == N + 1:
        return footprint

    for idx, country in enumerate(graph[key]):
        graph[key].pop(idx)

        tmp = footprint[:]
        tmp.append(country)
        print('tmp',tmp)

        ret = dfs(graph, N, country, tmp)

        graph[key].insert(idx, country)
        print(ret)
        if ret:
            return ret


def solution(tickets):
    answer = []

    graph = defaultdict(list)

    N = len(tickets)
    for ticket in tickets:
        graph[ticket[0]].append(ticket[1])
        graph[ticket[0]].sort()

    answer = dfs(graph, N, "ICN", ["ICN"])

    return answer
```


```python
solution([["ICN", "SFO"], ["ICN", "ATL"], ["SFO", "ATL"], ["ATL", "ICN"], ["ATL","SFO"]])
```

    ['ICN']
    tmp ['ICN', 'ATL']
    ['ICN', 'ATL']
    tmp ['ICN', 'ATL', 'ICN']
    ['ICN', 'ATL', 'ICN']
    tmp ['ICN', 'ATL', 'ICN', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO']
    tmp ['ICN', 'ATL', 'ICN', 'SFO', 'ATL']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL']
    tmp ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']
    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']





    ['ICN', 'ATL', 'ICN', 'SFO', 'ATL', 'SFO']




```python

```

---
title: "Programmers-Graph"
date: 2020-07-01 08:26:28 -0400
classes: wide
toc: true
categories: [Algorithm]
---

# 그래프

# 1. 가장 먼 노드

[문제 설명]

n개의 노드가 있는 그래프가 있습니다. 각 노드는 1부터 n까지 번호가 적혀있습니다. 1번 노드에서 가장 멀리 떨어진 노드의 갯수를 구하려고 합니다. 가장 멀리 떨어진 노드란 최단경로로 이동했을 때 간선의 개수가 가장 많은 노드들을 의미합니다.

노드의 개수 n, 간선에 대한 정보가 담긴 2차원 배열 vertex가 매개변수로 주어질 때, 1번 노드로부터 가장 멀리 떨어진 노드가 몇 개인지를 return 하도록 solution 함수를 작성해주세요.

[제한사항]
- 노드의 개수 n은 2 이상 20,000 이하입니다.
- 간선은 양방향이며 총 1개 이상 50,000개 이하의 간선이 있습니다.
- vertex 배열 각 행 [a, b]는 a번 노드와 b번 노드 사이에 간선이 있다는 의미입니다.

[입출력 예]

n	|  vertex	|  return
- 6	|  [[3, 6], [4, 3], [3, 2], [1, 3], [1, 2], [2, 4], [5, 2]]	|  3


```python
def solution(n,vertex):
    g =  {} #관계를 나타내는 딕셔너리를 만들기
    for v in vertex:
        if v[0] in g:
            g[v[0]].append(v[1])
        else:
            g[v[0]] = [v[1]]
            
    for i in vertex: #양방향으로 만들기 위해 반대 방향도 추가
        if i[1] in g:
            g[i[1]].append(i[0])
        else:
            g[i[1]] =[i[0]]
    
    dist = [] #거리를 나타내는 리스트
    qu= [] #앞으로 확인해야할 노드
    done =set() #이미 확인한 노드
    
    qu.append((1,0))
    done.add(1)
    
    while qu:
        p,d = qu.pop(0)
        
        for x in g[p]:
            if x not in done: #이미 연결된 점이 아니라면
                qu.append((x,d+1)) #앞으로 연결해야할 목록 update
                done.add(x) #연결된 목록 추가
                dist.append(d+1) #거리에 1을 추가해서 등록
    return sum([x==max(dist) for x in dist])
```


```python
solution(6,[[3, 6], [4, 3], [3, 2], [1, 3], [1, 2], [2, 4], [5, 2]] )
```




    3



# 2. 순위

[문제 설명]

n명의 권투선수가 권투 대회에 참여했고 각각 1번부터 n번까지 번호를 받았습니다. 권투 경기는 1대1 방식으로 진행이 되고, 만약 A 선수가 B 선수보다 실력이 좋다면 A 선수는 B 선수를 항상 이깁니다. 심판은 주어진 경기 결과를 가지고 선수들의 순위를 매기려 합니다. 하지만 몇몇 경기 결과를 분실하여 정확하게 순위를 매길 수 없습니다.

선수의 수 n, 경기 결과를 담은 2차원 배열 results가 매개변수로 주어질 때 정확하게 순위를 매길 수 있는 선수의 수를 return 하도록 solution 함수를 작성해주세요.

[제한사항]
- 선수의 수는 1명 이상 100명 이하입니다.
- 경기 결과는 1개 이상 4,500개 이하입니다.
- results 배열 각 행 [A, B]는 A 선수가 B 선수를 이겼다는 의미입니다.
- 모든 경기 결과에는 모순이 없습니다.

[입출력 예]

n	| results	|  return
- 5	|  [[4, 3], [4, 2], [3, 2], [1, 2], [2, 5]]	|  2


```python
def solution(n,results):  
    count = 0
    
    win ={} #이긴 관게는 저장하는 딕셔너리
    for i in results:
        if i[0] in win:
            win[i[0]].append(i[1])
        else:
            win[i[0]] = [i[1]]
            
    lose= {} #진 관계를 저장하는 딕셔너리
    for i in results:
        if i[1] in lose:
            lose[i[1]].append(i[0])
        else:
            lose[i[1]] = [i[0]]

            
    for i in range(1,n+1):
        done = set()
        qu = [i]
        qu_ = [i]

        while qu: #이긴 경우 dfs
            p = qu.pop(0)
            if p in win:
                for x in win[p]:
                    if x not in done:
                        done.add(x)
                        qu.append(x)
        while qu_: #진 경우  dfs
            p = qu_.pop(0)
            if p in lose:
                for x in lose[p]:
                    if x not in done:
                        done.add(x)
                        qu_.append(x)
        if len(done)>=n-1: #이긴 경우와 진 경우의 합이 나머지 선수의 수보다 크면 순위를 가릴 수 있음
            count +=1
    return count
```


```python
solution(5,[[4, 3], [4, 2], [3, 2], [1, 2], [2, 5]] )
```




    2



# 3. 방의 개수

[문제 설명]

원점(0,0)에서 시작해서 아래처럼 숫자가 적힌 방향으로 이동하며 선을 긋습니다.

![png](/images/programmers_files/g0.png)

ex) 1일때는 오른쪽 위로 이동

그림을 그릴 때, 사방이 막히면 방하나로 샙니다.
이동하는 방향이 담긴 배열 arrows가 매개변수로 주어질 때, 방의 갯수를 return 하도록 solution 함수를 작성하세요.

[제한사항]
- 배열 arrows의 크기는 1 이상 100,000 이하 입니다.
- arrows의 원소는 0 이상 7 이하 입니다.
- 방은 다른 방으로 둘러 싸여질 수 있습니다.

[입출력 예]

arrows	|  return
- [6, 6, 6, 4, 4, 4, 2, 2, 2, 0, 0, 0, 1, 6, 5, 5, 3, 6, 0]	|  3

![png](/images/programmers_files/g1.png)


1. 방문한 좌표와 이동경로를 key로 하는 dict를 2개 만들고 방문하면 1로 저장한다

2. 왔던 경로를 반대로 돌아가는 경로를 고려하여 경로를 저장하는 dir에 반대방향으로 이동하는 경로도 체크

3. X 자로 교차하여 방을 만드는 경우를 고려하여 한번 이동할때 2칸씩 이동. 이렇게 하면 X자로 교차되는 지점의 좌표를 지정할 수 있다
4. 다음 좌표가 이전에 방문한 적이 있고 이동한 적 없는 경로라면 방을 만들게 되므로 방의 개수를 증가

5. 그 외의 경우에는 좌표를 방문했음을 체크하고 경로도 체크

https://chldkato.tistory.com/101


```python
from collections import deque
dx = [-1, -1, 0, 1, 1, 1, 0, -1]
dy = [0, 1, 1, 1, 0, -1, -1, -1]

def solution(arrows):
    cnt = {}
    move = {}
    q = deque()
    cnt[(0,0)] = 0
    q.append([0,0])
    x,y,ans = 0,0,0
    
    for i in arrows:
        for j in range(2):
            nx = x + dx[i]
            ny = y + dy[i]
            cnt[(nx,ny)] = 0
            move[(x,y,nx,ny)] = 0
            move[(nx,ny,x,y)] = 0
            q.append([nx,ny])
            x,y = nx,ny
            
    x,y = q.popleft()
    cnt[(x,y)] = 1
    while q:
        nx, ny = q.popleft()
        
        if cnt[(nx,ny)] ==1:
            if move[(x,y,nx,ny)] ==0:
                ans +=1
                move[(x,y,nx,ny)] = 1
                move[(nx,ny,x,y)] = 1
        else:
            cnt[(nx,ny)] =1
            move[(x,y,nx,ny)] = 1
            move[(nx,ny,x,y)] = 1
                
        x,y = nx,ny
    return ans
```


```python
solution([6, 6, 6, 4, 4, 4, 2, 2, 2, 0, 0, 0, 1, 6, 5, 5, 3, 6, 0])
```




    3



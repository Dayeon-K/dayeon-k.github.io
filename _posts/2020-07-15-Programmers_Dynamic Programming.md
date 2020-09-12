---
title: "Programmers-DynamicProgramming"
date: 2020-07-15 08:26:28 -0400
classes: wide
toc: true
categories: Algorithm
---

# 동적 프로그래밍 (Dynamic Programming)

#https://blog.naver.com/shestory2015/221629833993

# 1. N으로 표현

[문제 설명]


아래와 같이 5와 사칙연산만으로 12를 표현할 수 있습니다.


12 = 5 + 5 + (5 / 5) + (5 / 5)

12 = 55 / 5 + 5 / 5

12 = (55 + 5) / 5


5를 사용한 횟수는 각각 6,5,4 입니다. 그리고 이중 가장 작은 경우는 4입니다.
이처럼 숫자 N과 number가 주어질 때, N과 사칙연산만 사용해서 표현 할 수 있는 방법 중 N 사용횟수의 최솟값을 return 하도록 solution 함수를 작성하세요.

[제한사항]
- N은 1 이상 9 이하입니다.
- number는 1 이상 32,000 이하입니다.
- 수식에는 괄호와 사칙연산만 가능하며 나누기 연산에서 나머지는 무시합니다.
- 최솟값이 8보다 크면 -1을 return 합니다.

[입출력 예]

N	|  number	|  return
- 5	|  12	|  4
- 2	|  11	|  3


```python
def solution(N, number):
    num = [[N]] #1개,2개 짜리
    for i in range(2,9):#i개짜리

        tmp =[]
        tmp += [int(str(N)*i)]
        for j in range(1,i): #(i-1 +1 i-2 + 2...)
            for x in num[i-j-1]: 
                for y in num[j-1]:
                    if y != 0:
                        tmp += [x+y]
                        tmp += [x-y]
                        tmp += [x*y]
                        tmp += [int(x/y)] 

        if number in tmp:
            return i
        num.append(tmp)
        print(i,num)
    return -1
```


```python
def solution(N, number):
    S = [{N}]
    for i in range(2, 9):
        lst = [int(str(N)*i)]
        for X_i in range(0, int(i / 2)):
            for x in S[X_i]:
                for y in S[i - X_i - 2]:
                    lst.append(x + y)
                    lst.append(x - y)
                    lst.append(y - x)
                    lst.append(x * y)
                    if x != 0: lst.append(y // x)
                    if y != 0: lst.append(x // y)
        if number in set(lst):
            return i
        S.append(lst)
    return -1
```

# 2. 정수 삼각형

![%EC%A0%95%EC%88%98%20%EC%82%BC%EA%B0%81%ED%98%95.png](attachment:%EC%A0%95%EC%88%98%20%EC%82%BC%EA%B0%81%ED%98%95.png)

위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.

삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.

[제한사항]
- 삼각형의 높이는 1 이상 500 이하입니다.
- 삼각형을 이루고 있는 숫자는 0 이상 9,999 이하의 정수입니다.

[입출력 예]

triangle	|  result
- [[7], [3, 8], [8, 1, 0], [2, 7, 4, 4], [4, 5, 2, 6, 5]]	|  30


```python
def solution(triangle):
    for i in range(1,len(triangle)):
        for j in range(len(triangle[i])):
            if j==0:
                triangle[i][j] += triangle[i-1][j]
            elif j==len(triangle[i])-1:
                triangle[i][j] += triangle[i-1][j-1]
            else:
                triangle[i][j] += max(triangle[i-1][j-1],triangle[i-1][j])
        #print(triangle[i])
    return max(triangle[len(triangle)-1])
```


```python
solution([[7], [3, 8], [8, 1, 0], [2, 7, 4, 4], [4, 5, 2, 6, 5]])
```

    [10, 15]
    [18, 16, 15]
    [20, 25, 20, 19]
    [24, 30, 27, 26, 24]





    30



# 3. 등교길

[문제 설명]

계속되는 폭우로 일부 지역이 물에 잠겼습니다. 물에 잠기지 않은 지역을 통해 학교를 가려고 합니다. 집에서 학교까지 가는 길은 m x n 크기의 격자모양으로 나타낼 수 있습니다.

아래 그림은 m = 4, n = 3 인 경우입니다.

![png](/images/programmers_files/sch.png)

가장 왼쪽 위, 즉 집이 있는 곳의 좌표는 (1, 1)로 나타내고 가장 오른쪽 아래, 즉 학교가 있는 곳의 좌표는 (m, n)으로 나타냅니다.

격자의 크기 m, n과 물이 잠긴 지역의 좌표를 담은 2차원 배열 puddles이 매개변수로 주어집니다. 집에서 학교까지 갈 수 있는 최단경로의 개수를 1,000,000,007로 나눈 나머지를 return 하도록 solution 함수를 작성해주세요.

[제한사항]
- 격자의 크기 m, n은 1 이상 100 이하인 자연수입니다.
- m과 n이 모두 1인 경우는 입력으로 주어지지 않습니다.
- 물에 잠긴 지역은 0개 이상 10개 이하입니다.
- 집과 학교가 물에 잠긴 경우는 입력으로 주어지지 않습니다.

[입출력 예]

m	|  n	|  puddles	|  return
- 4	|  3	|  [[2, 2]]	|4


```python
def solution(m,n,puddles):
    count = [[1 for x in range(m)] for y in range(n)]
    for p in puddles:
        count[p[1]-1][p[0]-1] = 0
    
    for i in range(1,m):
        if count[0][i-1] ==0:
            count[0][i] =0
    for i in range(1,n):
        if count[i-1][0] == 0:
            count[i][0] = 0
                
    for row in range(1,n):
        for col in range(1,m):
            if count[row][col] != 0:
                count[row][col] = count[row][col-1] + count[row-1][col]

    return count[n-1][m-1]%1000000007
```


```python
solution(4,3,[[2,2]])
```




    4



# 4. 도둑질

[문제 설명]

도둑이 어느 마을을 털 계획을 하고 있습니다. 이 마을의 모든 집들은 아래 그림과 같이 동그랗게 배치되어 있습니다.

![png](/images/programmers_files/theft.png)

각 집들은 서로 인접한 집들과 방범장치가 연결되어 있기 때문에 인접한 두 집을 털면 경보가 울립니다.

각 집에 있는 돈이 담긴 배열 money가 주어질 때, 도둑이 훔칠 수 있는 돈의 최댓값을 return 하도록 solution 함수를 작성하세요.

[제한사항]
- 이 마을에 있는 집은 3개 이상 1,000,000개 이하입니다.
- money 배열의 각 원소는 0 이상 1,000 이하인 정수입니다.

[입출력 예]

money	|  return
- [1, 2, 3, 1]	|  4


```python
def solution(money):
    
    first = [money[0],money[1],money[0]+money[2]] #처음 집을 도둑질 하는 경우, 마지막 집 못함
    for i in range(3,len(money)):
        first.append(money[i]+max(first[-2],first[-3])) #2번째전과 3번째 전 중 더 큰 값 가져옴
    first[-1] = max(first[i-2],first[i-3]) #마지막 집은 포함 안함
    
    last = [0,money[1],money[2]] #마지막 집을 도둑질 할 경우
    for i in range(3,len(money)):
        last.append(money[i]+max(last[-2],last[-3]))
    
    return max(first[-1],first[-2],last[-1],last[-2]) #네가지 중 가장 큰 값
```


```python
solution([1,2,3,1])
```




    4




```python
#다른 사람의 풀이
def solution(a):
    x1, y1, z1 = a[0], a[1], a[0]+a[2] #첫번째 집 도둑질
    x2, y2, z2 = 0, a[1], a[2] #첫번쨰 집 도둑질 안 함
    for i in a[3:]:
        x1, y1, z1 = y1, z1, max(x1, y1)+i #리스트 대신 객체 3개로 2개 전, 3개 전, 도둑질한 총 값 계속 업데이트
        x2, y2, z2 = y2, z2, max(x2, y2)+i
    return max(x1, y1, y2, z2)
```

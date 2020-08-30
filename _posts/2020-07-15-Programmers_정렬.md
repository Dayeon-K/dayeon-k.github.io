---
title: "Programmers-정렬"
date: 2020-07-15 08:26:28 -0400
classes: wide
toc: true
categories: Algorithm
---

# 정렬

https://programmers.co.kr/learn/courses/30/parts/12198

## K번째수

배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.

예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면

array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.
1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.
2에서 나온 배열의 3번째 숫자는 5입니다.
배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

제한사항
- array의 길이는 1 이상 100 이하입니다.
- array의 각 원소는 1 이상 100 이하입니다.
- commands의 길이는 1 이상 50 이하입니다.
- commands의 각 원소는 길이가 3입니다.


<br>
입출력 예<br>
array |	commands  |	return
- [1, 5, 2, 6, 3, 7, 4] |  	[[2, 5, 3], [4, 4, 1], [1, 7, 3]]	|  [5, 6, 3]


```python
#나의 풀이
def solution(array, commands):
    answer =[]
    for command in commands:
        answer.append(sorted(array[command[0]-1:command[1]])[command[2]-1])
    return answer
```


```python
#모범 답안 (lambda)
def solution(array, commands):
    return list(map(lambda x:sorted(array[x[0]-1:x[1]])[x[2]-1], commands))

```

## 가장 큰 수

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

제한 사항
- numbers의 길이는 1 이상 100,000 이하입니다.
- numbers의 원소는 0 이상 1,000 이하입니다.
- 정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

numbers | 	return
- [6, 10, 2] |	"6210"
- [3, 30, 34, 5, 9]|	"9534330"

Key
- 219 21- > 219219219, 212121 비교 (x3)
- 0으로 시작하는 경우 고려


```python
def solution(numbers):
    numbers = list(map(str, numbers))
    number.sort(reverse=True, key=lambda x:x*3)
    return str(int(''.join(numbers)))
```

## H-Index

H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과1에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 n편 중, h번 이상 인용된 논문이 h편 이상이고 나머지 논문이 h번 이하 인용되었다면 h의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.

제한사항
- 과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
- 논문별 인용 횟수는 0회 이상 10,000회 이하입니다.

citations| return
- [3, 0, 6, 1, 5] | 	3


```python
def solution(citations):
    n =len(citations)
    h = 0

    for i in range(n+1):
        count = len([x for x in citations if x>=i])
        if count>=i:
            h = i

    return h
```


```python
def solution(citations):
    citations.sort()
    n = len(citations)
    
    for ind,cit in enumerate(citations):
        if cit >= n- ind:
            return n-ind
    return 0
```

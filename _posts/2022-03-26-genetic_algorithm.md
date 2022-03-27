---
layout: post
title: 파이썬으로 유전 알고리즘 구현
subtitle: Python과 turtle을 이용해 간단하게 구현한 유전 알고리즘
tags: [Python, turtle, 인공지능]
thumbnail-img: /assets/img/GA1.png
---
파이썬과 turtle 라이브러리를 이용해 유전 알고리즘을 간단하게 구현해보았다.  

목표 : turtle을 목적지 (500, 500) 까지 가장 적은 횟수로 이동하며 도착하도록 만드는 것

일단 코드는 아래와 같이 작성했다.  
## main.py
```python
import random as rd
from practice import practice

great = 0.4         # 우수 개체 선정 비율
childs = 5          # 한 쌍의 부모 개체들의 자식 개체 수
mutation_p = 5      # 변이 확률(%, 정수)
aw = 1/10           # attempts에 부여되는 가중치
initial_generation = 100   # 첫 세대 개체 수
n_generation = 15  # 몇 세대까지 진행할건지


def distance(c):
    dis = 500000-((500-c[0])**2 + (500-c[1])**2)
    return dis


with open("log.txt", "w") as g:
    g.write("")


def create_child(p1, p2):
    child = []
    min_len_parent = max(len(p1), len(p2))
    for i in range(min_len_parent):
        if (int(rd.randint(1, 100)) > 50):
            if i+1 > len(p1):
                child.append(rd.choice([90, 180, 270, 360]))
            else:
                child.append(p1[i])
        else:
            if i+1 > len(p2):
                child.append(rd.choice([90, 180, 270, 360]))
            else:
                child.append(p2[i])
    return child


def create_children(parents, n_child):
    next_population = []
    for i in range(int(len(parents)/2)):
        for j in range(n_child):
            next_population.append(create_child(
                parents[i][3], parents[len(parents)-1-i][3]))
    return next_population


def mutate(m):
    dirs = [0, 90, -90]
    m[rd.randint(0, len(m)-1)] = rd.choice(
        dirs)         # history 중에 하나를 랜덤으로 바꿈
    return m


def mutated_population(normal_population, probability):
    for i in range(len(normal_population)):
        if rd.randint(1, 100) <= probability:
            normal_population[i] = mutate(normal_population[i])
    return normal_population


def go(direction):
    global current_coordinates
    if direction == 90:
        current_coordinates[1] += 50
    elif direction == 270 or direction == -90:
        current_coordinates[1] -= 50
    elif direction == 360 or direction == 0:
        current_coordinates[0] += 50
    elif direction == 180:
        current_coordinates[0] -= 50


def processing(current_coordinates, attempts, history):
    score = ((distance(current_coordinates)/(attempts**aw))) / \
        (500000/20**aw)*100

    history_to_append = ([current_coordinates, distance(
        current_coordinates), attempts, history, score])

    return [score, history_to_append]


histories = []      # [좌표, 거리, 시도횟수, 기록, 점수]
current_coordinates = [0, 0]
final_result = []   # 최종적으로 시뮬레이션되는 개체들

for i in range(initial_generation):         # 초깃값 생성
    history = []
    current_coordinates = [0, 0]
    attempts = 0
    while True:
        direction = rd.randint(1, 4)*90
        if (0 <= current_coordinates[0] <= 500) and (0 <= current_coordinates[1] <= 500) and (current_coordinates != [500, 500]):
            go(direction)
            history.append(direction)
            attempts = attempts + 1
        else:
            break

    score = processing(current_coordinates, attempts, history)[0]
    histories.append(processing(current_coordinates, attempts, history)[1])


histories_sorted = sorted(histories, key=lambda x: x[4], reverse=True)
histories_sorted = histories_sorted[:round((len(histories_sorted)+1)*great)]
rd.shuffle(histories_sorted)


new_generation_basis = create_children(histories_sorted, childs)
new_generation_basis = mutated_population(new_generation_basis, mutation_p)
print(new_generation_basis)

for i in range(n_generation-1):      # 세대 수

    histories = []      # [좌표, 거리, 시도횟수, 기록, 점수]
    current_coordinates = [0, 0]

    for i in new_generation_basis:
        history = []
        current_coordinates = [0, 0]
        attempts = 0

        for j in i:
            if (0 <= current_coordinates[0] <= 500) and (0 <= current_coordinates[1] <= 500) and (current_coordinates != [500, 500]):
                go(j)
                history.append(j)
                attempts = attempts + 1

        score = processing(current_coordinates, attempts, history)[0]
        histories.append(processing(current_coordinates, attempts, history)[1])

    histories_sorted = sorted(histories, key=lambda x: x[4], reverse=True)
    histories_sorted = histories_sorted[:round(
        (len(histories_sorted)+1)*great)]
    rd.shuffle(histories_sorted)

    new_generation_basis = create_children(histories_sorted, childs)
    new_generation_basis = mutated_population(new_generation_basis, mutation_p)
    print(new_generation_basis)         # 교배, 변이까지 마친 새 세대
    print(len(new_generation_basis))

    final_result.append(histories_sorted[0])    # 해당 세대에서 가장 뛰어난 한 개체를 선출


for i in new_generation_basis:
    history = []
    current_coordinates = [0, 0]
    attempts = 0

    for j in i:
        if (0 <= current_coordinates[0] <= 500) and (0 <= current_coordinates[1] <= 500) and (current_coordinates != [500, 500]):
            go(j)
            history.append(j)
            attempts = attempts + 1

    score = processing(current_coordinates, attempts, history)[0]
    histories.append(processing(current_coordinates, attempts, history)[1])

histories_sorted = sorted(histories, key=lambda x: x[4], reverse=True)
histories_sorted = histories_sorted[:3]

print(histories_sorted)
final_result.append(histories_sorted[0])    # 해당 세대에서 가장 뛰어난 한 개체를 선출

final_result_toPractice = []
o = 0
while o < len(final_result):
    final_result_toPractice.append(final_result[o])
    o += 1
practice(final_result_toPractice)
input("")       # 엔터 입력하면 꺼짐
```  


## practice.py
```python
from tkinter import font
import turtle
import time

t = turtle.Turtle()
t2 = turtle.Turtle()
text = turtle.Turtle()
text2 = turtle.Turtle()


def practice(pos):
    for i in range(len(pos)):
        t2.pendown()
        text.write(f'{i+1} Generation', font=(10))
        text2.write(f'Score : {round(pos[i][4], 1)}', font=(10))
        for j in pos[i][3]:
            t2.setheading(j)
            t2.forward(50)
        t2.penup()
        t2.goto(0, 0)
        t2.clear()
        text.clear()
        text2.clear()

        time.sleep(0.1)


t.ht()
text.ht()
text2.ht()
t.pendown()
text2.penup()
t.shape("turtle")
t2.shape("turtle")
t2.shapesize(3)
text.color('red')
text2.setposition(0, -50)
t2.speed(4)
t.color('red')
t.speed(10)
t.forward(500)
t.left(90)
t.forward(500)
t.left(90)
t.forward(500)
t.left(90)
t.forward(500)
t.left(90)
t.penup()
```  
[GitHub](https://github.com/jcy1511/genetic_algorithm)  
(처음 만들어본거라 코드가 조금 비효율적일 수도 있습니다)  

<br/>  
- - -  
Score : (총거리 - (원점~목적지 거리))/이동횟수^0.1  
우수 개체 선정 비율 : 40%  
자식 수 : 5  
변이 확률 : 5%  
첫 세대 개체 수 : 100  
15세대까지 진행했다.  

이동방향을 리스트로 저장해서 유전자로 사용했다.  

원래 처음에는 점수 계산을 단순하게 거리/이동횟수 로 했었는데  
이렇게 하니까 이동횟수에 너무 큰 가중치가 부여되어버렸고, 그에 따라 **덜 이동하는 것이 점수가 높게 나와버려서** 개체들이 목적지로 가지 않고 이동을 멈췄다.  

그래서 이동거리에 더 큰 가중치를 부여하기 위해 거리에 거듭제곱을 해줬지만 오류가 발생했다...   
그래서 그냥 분자를 제곱하는 대신에 분모에 루트를 씌워줬더니 정상적으로 작동했고, 조금 더 나은 결과를 냈다.   

작동 영상 :  
<div style="position:relative;height:0;padding-bottom:56.25%">
    <iframe src="https://www.youtube.com/embed/xF5HYXc3qn4"
    style="position:absolute;width:100%;height:100%;left:0"
    frameborder="0" allowfullscreen></iframe>
</div>
재밌네요 ㅇㅇ
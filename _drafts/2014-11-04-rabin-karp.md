---
layout:		post
comments:	true
title:		Алгоритм Рабина-Карпа
date:		2014-11-04 10:00:00
categories:
- algorithm
---

Трудней всего начать, и именно поэтому, чтобы долго не выбирать, начну описывать то, что изучил недавно - алгоритм Рабина-Карпа для решения следующей, классической задачи: 

> дана строка `S` и строка `P`, требуется выяснить, содержит ли строка`S` строку `P`, и если да, то вернуть позицию, с которой строка `P` начинается в строке `S`.

Могут быть вариации на тему того, сколько именно строк `P` содержится в строке `S`, но это уже легко находится, если известен базовый принцип.

Работа данного алгоритма основывается на эффективной технике, которая называется хеширование, которой стоит посвятить отдельный конспект, но для текущего достаточно знать, что эта техника позволяет эффективно конвертировать строку в число.

Для простоты примера, будем рассматривать строки, состоящие из цифр, например `"34127"`, и примем, что хеш таких строк равен самому числу т.е. `hash("34127")=34127`.

Алгоритм базируется на следующих 2х свойствах, которые особенно хорошо заметны на строках состоящих из цифр:

1. Если нам известен хеш текущей подстроки длины `N`, то мы можем найти хеш для подстроки длины `N + 1`. Например, для строки `"34127"`, ее подстрока `"34"` имеет `hash("34")=34`, а подстрока `"341"` имеет `hash("341")=341`.
2. Если нам известен хеш подстроки длины `N`, которая начинается с индекса `shift`, то мы можем найти хеш подстроки длиной `N` начинающегося с индекса `shift+1`. Например, для строки `"34127"`, ее подстрока `"34"`, (shift=0) имеет `hash("34")=34`, а подстрока `"41"` (shift=1) имеет `hash("41")=41`.


Отсюда алгоритм:

>Посчитать хеш искомой подстроки `P`, посчитать хеш подстроки текста `S` длиной length(`P`), начиная с начала текста (позиция 0), если хеши совпадают - ответ найден, если нет - сместить начало подстроки текста `S` на 1 и повторить расчет. Алгоритм продолжается пока не найдена искомая подстрока `P` или достигнут конец текста `S`.

>Часто, подстроку текста `S` длиной length(`P`) называют __"окно"__, через которое, мы как бы смотрим на исходный текст в поисках подстроки `P`, но имеем ограниченный угол обзора, равный length(`P`).

Например, рассмотрим случай когда строка `S="34127"` и искомая подстрока `P="12"`.  
Схематически:
{% highlight ruby linenos %}
34127
||		<- shift = 0
12

34127
 ||		<- shift = 1
 12

34127
  ||		<- shift = 2
  12
{% endhighlight %}

И чуть более формализованно:
{% highlight bash linenos %}
char: | 3 | 4 | 1 | 2 | 7 |
index:| 0 | 1 | 2 | 3 | 4 |

// Хеш подстроки
hash(P{"12"})=12

// Хеш подстроки длиной |P| в исходной строке S
hash(S{"34"})=34

// Подстрока находится в начале строки
if hash(P{"12"})=12 == hash(S{"34"})=34 return shift(0)

// Расчет хешей со смещением
shift: 1, hash(S{"41"}) = 41 => 41 != 12
shift: 2, hash(S{"12"}) = 12 => 12 == 12 return shift(2)
{% endhighlight %}

В строках `5` и `8` рассчитываются хеши искомой подстроки(`P`) и хеш подстроки длиной length(`P`) текста `S`. Если они совпадают, то это означает что подстрока найдена, и она начинается с позиции `0` (строка `11`).

Легко заметить, что на каждом этапе для расчета нужного хеша со смещением, нам во-первых, нужно избавиться от первой цифры хеша, во-вторых, сместить оставшееся значение на length(`P`) - 1 позиций, и добавить хеш текущего числа.
Например, для того чтобы из hash(S{"34"}) получить hash(S{"41"}):

1. newHash = hash(S{"34"}) - '3' = "4"
2. newHash = newHash * 10^(length('P')) = 4  * 10 ^ 1 = 40
3. newHash = newHash + hash("1") = 40 + 1 = 41

Это то что лежит в основе алгоритма, в жизни используется такой же принцип, с той лишь разницей, что мы не ограничиваемся строками состоящими только из цифр, и используем немного другую хеш функцию.

TODO: добавить реализацию.

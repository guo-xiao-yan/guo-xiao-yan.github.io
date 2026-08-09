---
layout: post
title:  "2022瑞信code challenge"
date:   2022-09-22 18:37:21 +0800
categories: Contest CUHK
---

[2022 Credit Suisse Coding Challenge - CodeIT Suisse Hong Kong](https://creditsuisse.recsolu.com/app/collect/event/YI8jZwhHZ3yE6bXaBGT7VA)
![team](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/team.jpg)
我：右一，队友：右二&右三

# 介绍
我们的队伍：SmilyRainInBerlin
![name](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/name-tag.jpg)

赛区：香港，团队赛
使用语言：Python
我们的排名：5
![rank](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/rank.jpg)

我交的代码：[@Tangent617/guoxiaoyan-flask](https://replit.com/@Tangent617/guoxiaoyan-flask)

队友交的代码：[@Lxydouble7/lxy](https://replit.com/@Lxydouble7/lxy)、[@BerlinkR/RongBailinHKUSmilyRainInBerlin](https://replit.com/@BerlinkR/RongBailinHKUSmilyRainInBerlin)

# 题解
## Ticker Stream
### 题意
热身赛的题，唯一一道找到[原题链接](https://www.credit-suisse.com/content/dam/pwp/assets/careers/docs/pages/2022-codeItsuisse-entry-challenge.pdf)的。与正式比赛看到的略有不同，但是大意是这么回事。
### 思路
结构体（这玩意在python里好像用字典）排序。
json文件怎么读写是现查的。
感谢队友开了个先河让我知道怎么用flask做api交题。
### 代码
[@Tangent617/guoxiaoyan-flask#shitPile.py](https://replit.com/@Tangent617/guoxiaoyan-flask#shitPile.py)的前两坨，屑队友跟熊瞎子掰苞米一样交完这题写下一题就把前面的给删了，幸亏我存了一份。
## Crypto Collapz
### 题意
给一个数x，不断进行以下变换：
- x为奇数，x=x*3+1
- x为偶数，x=x/2

问它能达到的最大值。
### 思路
队友写超时了，所以我决定打表。
前面算过的值后面直接用，范围内所有答案一存，一口气全读进去，问哪个返回哪个。
### 代码
打表部分：

```python
import json

'''
@app.route('/cryptocollapz',methods=['GET','POST'])
def cryptocollapz():
    data = request.get_json()
    data = json.loads(data)

    # solve
    print("request_for_cryptocollapz")
    print(data)
    dabiao()
    return cryptocollapz_solve(data)
'''
max_num = 1000000 + 5
mp = {}
mp[1] = 4
mp[2] = 4

def fun(i):
    if i in mp.keys():
        mp[i] = max(i, mp[i])
        return mp[i]
    else:
        mp[i] = i
        if (i & 1):
            i1 = i * 3 + 1
        else:
            i1 = int(i / 2)

        mp[i] = max(mp[i], fun(i1))

        return mp[i]

def dabiao():
    for i in range(1, max_num):
        mp[i] = fun(i)

file_dabiao = open("dabiao.txt", "r+")
str_dabiao = file_dabiao.read()
mp = json.loads(str_dabiao)

def cryptocollapz_solve(data):
    result_list = []
    for numlist in data:
        result = []
        for num in numlist:
            result.append(mp[str(num)])
        result_list.append(result)
    return Response(json.dumps(result_list),  mimetype='application/json')

dabiao()

f = open('dabiao.txt', 'w')
f.write(json.dumps(mp))
f.close()
```

本地运行得到一个`dabiao.txt`，传服务器上，再运行[@Tangent617/guoxiaoyan-flask#shitPile.py](https://replit.com/@Tangent617/guoxiaoyan-flask#shitPile.py)最后那两坨读取其中的值返回。
## Rubiks Cube
### 题意
给一个三阶魔方的初始状态和一系列转动操作，求最终状态。
### 思路
感谢这位佬提供的参考：[python实现三阶魔方还原的示例代码](http://www.zzvips.com/article/199188.html)
思维很简单就是很恶心心的一道题，丢给队友搞了。
~~代码可能也让队友给扔了。~~
## Travelling Suisse Robot
### 题意
给个地图，上面有一堆字母，让你走，最终拼出来`CODEITSUISSE`。

```
                                 \n 
               D     E      I    \n 
                                 \n 
               O                 \n 
                                 \n 
               C            T    \n 
                                 \n 
               X                 \n 
                                 \n 
   E S   S       I     U    S    \n 
                                 \n
```

你的操作可以是直走、左拐右拐、捡字母。只有直走算步数，求最短路径。答案不唯一可以输出多个，但是不可以转太多圈。

![discord](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/discord-1.png)

### 思路
DFS
### 代码
这是我在本地写的：

```python
from collections import deque

def strToArr(str):
    arr = str.split('\n')
    brr = []
    for s in arr:
        if len(s) != 0:
            brr.append(list(s))
    return brr

str = '                                 \n               D     E      I    \n                                 \n               O                 \n                                 \n               C            T    \n                                 \n               X                 \n                                 \n   E S   S       I     U    S    \n                                 \n'
goal = 'XCODEITSUISSE'
mp = strToArr(str)

n = len(mp)
m = len(mp[0])

C = []
O = []
D = []
E = []
I = []
T = []
S = []
U = []

for i in range(0, n):
    for j in range(0, m):
        if mp[i][j] == 'X':
            X = (i, j, 0)
        elif mp[i][j] == 'C':
            C.append((i, j, len(C)))
        elif mp[i][j] == 'O':
            O.append((i, j, len(O)))
        elif mp[i][j] == 'D':
            D.append((i, j, len(D)))
        elif mp[i][j] == 'E':
            E.append((i, j, len(E)))
        elif mp[i][j] == 'I':
            I.append((i, j, len(I)))
        elif mp[i][j] == 'T':
            T.append((i, j, len(T)))
        elif mp[i][j] == 'S':
            S.append((i, j, len(S)))
        elif mp[i][j] == 'U':
            U.append((i, j, len(U)))

letters_num = 0
vis = {}
vis[X] = True
minSteps = 10000000000
pathStack = []
pathStack.append(X)
minPath = []

def dis(node1, node2):
    return max(node1[0], node2[0]) - min(node1[0], node2[0]) + max(node1[1], node2[1]) - min(node1[1], node2[1])

def dfs(curnode, letters_num, steps):
    global minSteps, pathStack, minPath
    if letters_num == 0:
        for c in C:
            if c not in vis.keys() or not vis[c]:
                vis[c] = True
                pathStack.append(c)
                dfs(c, 1, steps + dis(curnode, c))
                vis[c] = False
                pathStack.pop()
    elif letters_num == 1:
        for o in O:
            if o not in vis.keys() or not vis[o]:
                vis[o] = True
                pathStack.append(o)
                dfs(o, 2, steps + dis(curnode, o))
                vis[o] = False
                pathStack.pop()
    elif letters_num == 2:
        for d in D:
            if d not in vis.keys() or not vis[d]:
                vis[d] = True
                pathStack.append(d)
                dfs(d, 3, steps + dis(curnode, d))
                vis[d] = False
                pathStack.pop()
    elif letters_num == 3:
        for e in E:
            if e not in vis.keys() or not vis[e]:
                vis[e] = True
                pathStack.append(e)
                dfs(e, 4, steps + dis(curnode, e))
                vis[e] = False
                pathStack.pop()
    elif letters_num == 4:
        for i in I:
            if i not in vis.keys() or not vis[i]:
                vis[i] = True
                pathStack.append(i)
                dfs(i, 5, steps + dis(curnode, i))
                vis[i] = False
                pathStack.pop()
    elif letters_num == 5:
        for t in T:
            if t not in vis.keys() or not vis[t]:
                vis[t] = True
                pathStack.append(t)
                dfs(t, 6, steps + dis(curnode, t))
                vis[t] = False
                pathStack.pop()
    elif letters_num == 6:
        for s in S:
            if s not in vis.keys() or not vis[s]:
                vis[s] = True
                pathStack.append(s)
                dfs(s, 7, steps + dis(curnode, s))
                vis[s] = False
                pathStack.pop()
    elif letters_num == 7:
        for u in U:
            if u not in vis.keys() or not vis[u]:
                vis[u] = True
                pathStack.append(u)
                dfs(u, 8, steps + dis(curnode, u))
                vis[u] = False
                pathStack.pop()
    elif letters_num == 8:
        for i in I:
            if i not in vis.keys() or not vis[i]:
                vis[i] = True
                pathStack.append(i)
                dfs(i, 9, steps + dis(curnode, i))
                vis[i] = False
                pathStack.pop()
    elif letters_num == 9:
        for s in S:
            if s not in vis.keys() or not vis[s]:
                vis[s] = True
                pathStack.append(s)
                dfs(s, 10, steps + dis(curnode, s))
                vis[s] = False
                pathStack.pop()
    elif letters_num == 10:
        for s in S:
            if s not in vis.keys() or not vis[s]:
                vis[s] = True
                pathStack.append(s)
                dfs(s, 11, steps + dis(curnode, s))
                vis[s] = False
                pathStack.pop()
    elif letters_num == 11:
        for e in E:
            if e not in vis.keys() or not vis[e]:
                vis[e] = True
                pathStack.append(e)
                dfs(e, 12, steps + dis(curnode, e))
                vis[e] = False
                pathStack.pop()
    elif letters_num >= 12:
        if (steps < minSteps):
            minSteps = steps
            minPath = pathStack.copy()
        return

dfs(X, 0, 0)
print(minSteps)
print(minPath)
    
def walk(node1, node2):
    walkPath = ''
    if node2[1] > node1[1]:
        walkPath = walkPath + 'R' + 'S' * (node2[1] - node1[1]) + 'L'
    if node2[1] < node1[1]:
        walkPath = walkPath + 'L' + 'S' * (node1[1] - node2[1]) + 'R'
    if node2[0] > node1[0]:
        walkPath = walkPath + 'LL' + 'S' * (node2[0] - node1[0]) + 'RR'
    if node2[0] < node1[0]:
        walkPath = walkPath + 'S' * (node1[0] - node2[0])
    walkPath = walkPath + 'P'
    return walkPath

wholePath = ''
for i in range(0, 12):
    wholePath += walk(minPath[i], minPath[i+1])
print(wholePath)
```

可以简略一点的，但是我懒了。
由于之前打ACM的习惯用了很多全局变量，请大家不要学我。
提交版本：[@Tangent617/guoxiaoyan-flask#BigPieceofShit.py](https://replit.com/@Tangent617/guoxiaoyan-flask#BigPieceofShit.py)

## Magic Cauldrons
### 题意
给一堆水杯，摆成三角形，从上往下数第一层1个，第二层2个，第n层n个这样。
从上往下倒水，杯子满了会向两边以相同速度溢出。
#### part1
每个杯子一样容量，给出倒水的速度和时间，求某个给定杯子的水量。
#### part2
每个杯子一样容量，给出某个给定杯子的水量和倒水速度，求倒水时间。
#### part3
每个杯子不一样容量，给出倒水的速度和时间，求某个给定杯子的水量。
#### part4
每个杯子不一样容量，给出某个给定杯子的水量和倒水速度，求倒水时间。
### 思路
part1和3模拟，队友写的：[@BerlinkR/RongBailinHKUSmilyRainInBerlin#magic.py](https://replit.com/@BerlinkR/RongBailinHKUSmilyRainInBerlin#magic.py)
part2和4队友又干超时了，丢给我写的，我想法是二分答案。
### 代码
经过我补完的版本：[@Tangent617/guoxiaoyan-flask#teammatesShit.py](https://replit.com/@Tangent617/guoxiaoyan-flask#teammatesShit.py)
## Social Distancing
### 题意
给一个矩形区域，和n个人想进去坐着。但是由于疫情原因，一个人周围8个格子不可以坐其他人。
如果已经坐在里面的人（给定了他们的位置）就不遵守规则，那就让他们自作自受，新加入的人要满足周围不能坐人的规则。
问多少种情况。
### 思路
队友又写崩了，又丢给我了。
我又想用DFS了。
### 代码
[@Tangent617/guoxiaoyan-flask#luckyShit.py](https://replit.com/@Tangent617/guoxiaoyan-flask#luckyShit.py)
## Swiss Stig
### 题意
有一个数，一个人问问题另一个回答。
- 问的人问这个数在不在【lower, upper】的区间里？
- 回答的人只会回答是和否。

问了多个问题后，问猜中的概率。
以上为第一问，第二问没做。
### 思路
#### warm-up
（总点数-问过的左端点数）/总点数
~~我也忘了题了，我也忘了我为啥这么想的了。~~
#### full
出题人关于第二问的提示：
![可达鸭的提示](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/discord-2.png)

例子：
![可达鸭的举例](/assets/images/2022CreditSuisseCodingChallenge-CodeITSuisseHongKong/discord-3.png)

### 代码
第一问部分：[@Tangent617/guoxiaoyan-flask#BeautifulShit.py](https://replit.com/@Tangent617/guoxiaoyan-flask#BeautifulShit.py)
## Time Travelling Stonks Man
### 题意
一个有时间穿越能力的人可以选在某年买哪个股票再在哪年卖，问能得到的最大价值。
### 思路
队友写的，好像在猜测试用例。我困死了在摸鱼睡大觉。
### 代码
[@Lxydouble7/lxy#main.py](https://replit.com/@Lxydouble7/lxy#main.py)
# FAQ

Q：为什么有3种链接？

A：因为我们仨是用三个电脑分开写的，三个链接都可以交。

Q：为什么没有详细的问题描述？

A：因为比完赛太困了睡了几天才缓过来，等想写题解时候再打开[比赛网站](https://cis2022-hk-team.herokuapp.com)发现🈚️了，看不到题是啥了，只能依靠可怜的回忆了。

Q：为什么部分我们做出的题如Calendar Days、DNS Cache和Quordle Keybord没写题解？

A：因为我没有参与这些题的解决过程，且既找不到题，也找不到队友写的代码，discord上的聊天记录信息不足。

Q：为什么在开源在[replit](https://replit.com)平台而不是[GitHub](https://github.com)？

A：因为一开始让用这个提交（虽然后来又改让用什么Heroku，但是我们直接搭个flask就整上了），懒得把代码移植合并过去了。

Q：为什么用简体中文？

A：因为这是爷的博客，爷就乐意用母语写。

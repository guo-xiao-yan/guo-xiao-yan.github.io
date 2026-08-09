---
layout: post
title:  "识别无符号数并计算其值"
date:   2021-04-18 11:22:38 +0800
categories: Compiler
---

编译原理作业exercise-2 1.5
![](/assets/images/recognize-an-unsigned-number-and-calculate-its-value/cover.png)

# 要求
1.	在程序内采用二维数组保存识别无符号数的状态转换矩阵;
2.	在识别无符号数的过程中，实现把输入的无符号数字符串转换成数值显示(不能使用类似atoi等函数实现);
3.	要采用矩阵查表的方式进行状态切换和执行语义动作，而不是采用switch-case语句进行;
# 思路
1.	认为输入的字符串格式为`s = a . b E +/- c`，分别用3个变量a, b, c保存它们的值
2.	使用`bool point, E, neg;`记录是否出现小数点、E和负号
3.	使用邻接矩阵`char state_trans[7][7]`保存状态转移表
`state_trans[i][j]`代表从状态i转移到状态j需要的字符
4.	函数`char simplify(char c)`将读入的符号简化，方便用一个字符记录以便在矩阵中查询
（1）	将数字1~9统一为d
（2）	将E与e统一为E
（3）	将+与-统一为+
5.	函数`void add_digit(char ch, int now_state)`根据当前读入的字符和状态计算更新a, b c, point, E, neg的值
（1）	读入.则将point置true
（2）	读入E或e将E置true
（3）	读入-将neg置true
（4）	读入数字则根据当前的不同状态更新a, b, c的值
6.	函数`double cal_ans()`根据已经得到的a, b, c的值计算出最终结果
（1）	不断将b除以10直到其<1得到小数位，初始ans置为a+b。
（2）	根据c的值对ans进行移位
7.	函数`double dfs(int pos, int state)`通过深度优先搜索遍历所有状态
（1）	若字符串全部遍历结束并且状态为最终态，则结束dfs得到答案
（2）	否则遍历6个状态分别判断是否可以到达，若能则更新状态进行下一步dfs

# 实现
## 代码

```c++
#include <iostream>
#include <string>
using namespace std;

string s;
// s = a . b E +/- c
int a = 0, b = 0, c = 0;
bool point = false, E = false, neg = false;
char state_trans[7][7] = {
    '0', 'd', '0', '.', '0', '0', '0',
    '0', 'd', '.', '0', 'E', '0', '0',
    '0', '0', 'd', '0', 'E', '0', '0',
    '0', '0', 'd', '0', '0', '0', '0',
    '0', '0', '0', '0', '0', '+', 'd',
    '0', '0', '0', '0', '0', '0', 'd', 
    '0', '0', '0', '0', '0', '0', 'd'
};

char simplify(char c) {
    if (c >= '0' && c <= '9') return 'd';
    if (c == 'E' || c == 'e') return 'E';
    if (c == '+' || c == '-') return '+';
    return c;
}

void add_digit(char ch, int now_state) {
    if (ch == '.')
        point = true;
    else if (ch == 'E')
        E = true;
    else if (ch == '+' || ch == '-') {
        if (ch == '-')
            neg = true;
    } else if (ch >= '0' && ch <= '9') {
        int digit = ch - '0';
        if (now_state == 0 || now_state == 1)
            a = a * 10 + digit;
        else if (now_state == 2 || now_state == 3)
            b = b * 10 + digit;
        else if (now_state == 4 || now_state == 5 || now_state == 6)
            c = c * 10 + digit;
    } else {
        cerr << "invalid input!" << endl;
    }
}

void test() {
    cout << a;
    if (point)
        cout << '.';
    else
        cout << ' ';
    cout << b;
    if (E)
        cout << 'E';
    else
        cout << ' ';
    if (neg)
        cout << '-';
    else
        cout << '+';
    cout << c << endl;
}

double cal_ans() {
    //test();

    double bb = b, ans;
    while (bb > 1)
        bb /= 10;
    ans = a + bb;

    if (!E) { // a.b
        return ans;
    } else if (!neg) { // a.bEc
        for (int i = 0; i < c; i++) {
            ans *= 10;
        }
        return ans;
    } else { // a.bE-c
        for (int i = 0; i < c; i++) {
            ans /= 10;
        }
        return ans;
    }
}

double dfs(int pos, int state) {
    if (pos == s.length() - 1 && (state == 1 || state == 2 || state == 6))
        return cal_ans();
        
    for (int next_state = 0; next_state < 7; next_state++) {
        if (state_trans[state][next_state] == simplify(s[pos+1])) {
            int tmp_a = a, tmp_b = b, tmp_c = c;
            bool tmp_point = point, tmp_E = E, tmp_neg = neg;
            add_digit(s[pos+1], state);
            return dfs(pos + 1, next_state);
            a = tmp_a, b = tmp_b, c = tmp_c;
            point = tmp_point, E = tmp_E, neg = tmp_neg;
        }
    }
    return -1;
}

int main(int argc, const char * argv[])
{
    cin >> s;
    cout << dfs(-1, 0) << endl;
    return 0;
}
```

## 编译

```bash
cd exercise-2
g++-10 main.cpp -o main
```

## 运行

```bash
./main
```

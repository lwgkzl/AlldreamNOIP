﻿# 字串变换

## 题目描述

已知有两个字串A,B及一组字串变换的规则（至多6个规则）:  
A1->B1  
A2->B2	   
规则的含义为：在 A中的子串 A1可以变换为 B1，A2可以变换为B2…。  
例如：A='abcd'，B＝'xyz'  
变换规则为：  
‘abc’->‘xu’ ；     ‘ud’->‘y’ ；    ‘y’->‘yz’ ；  
则此时，A可以经过一系列的变换变为B，其变换的过程为：  
‘abcd’->‘xud’->‘xy’->‘xyz’  
共进行了3次变换，使得A变换为B。

## 输入格式

A B  
A1 B1  
A2 B2  
……  
所有字符串长度的上限为20。   

## 输出格式

若在10步（包含10步）以内能将A变换为B，则输出最少的变换步数；否则输出"NO ANSWER!"。

## 输入样例

```	 
abcd xyz  
abc xu  
ud y  
y yz     
```    

## 输出样例  

```		
3     
```   

## 测试网站  	

[P1032](https://www.luogu.org/problemnew/show/P1032)  	 

## 题目分析  	

这道题给出初始状态，结尾状态和变换规则，求最小变换步数。很容易想到用bfs来解决。这里为了提高效率我用的双向bfs，即从起始状态和终止状态同时搜，直到两者相遇。
双向bfs需要开两个队列，一个从前往后入队搜索，一个从后往前入队搜索；再用两个map记录已搜过的状态，一是用来判该状态是否已访问过，二是存储该状态的变换步数。
然后是字符串变换，可以用string的substr和replace，具体用法见代码实现。  

## 代码示例  

```c++	
#include<bits/stdc++.h>
using namespace std;
#define Max 10
struct Node{
    string s;
    int deep;
};
string a, b;
string A[Max], B[Max];
int n=0;
map<string, int> map1, map2;//分别记录从前往后搜和从后往前搜的状态，map的值为当前状态变换的步数
queue<Node> q1, q2;//两个队列，一个从初始状态开始搜，一个从最终状态搜
int ans = 0;
string Change1(string s, int i, int j){//正着转换字符串
    int l = A[j].length();
    if(i+l > s.length())return "";
    string s1 = s.substr(i, l);
    if(s1 == A[j]){
        s.replace(i, l, B[j]);
        return s;
    }
    else return "";
}
string Change2(string s, int i, int j){//倒着转换字符串
    int l = B[j].length();
    if(i+l > s.length())return "";
    string s1 = s.substr(i, l);
    if(s1 == B[j]){
        s.replace(i, l, A[j]);
        return s;
    }
    else return "";
}
void bfs(){
    map1.clear(), map1[a] = 0;//将初始状态存下来
    map2.clear(), map2[b] = 0;//将结尾状态存下来
    Node now;
    now.s = a;
    now.deep = 0;
    q1.push(now);
    while(!q1.empty() || !q2.empty()){
        if(!q1.empty()){
            now = q1.front();
            q1.pop();
            if(now.deep > 6){
                return ;
            }
            for(int i=0; i<now.s.length(); i++){
                for(int j=0; j<n; j++){
                    string s1 = Change1(now.s, i, j);
                    if(s1 == "")continue;
                    int d = now.deep + 1;
                    if(map2.count(s1)){//在后往前搜过的状态里找到当前的状态，
                                        //说明两中搜索相遇，这时两个步数相加即是答案
                        ans = map2[s1] + d;
                        return ;
                    }
                    else{
                        Node tmp = {s1, d};
                        q1.push(tmp);
                        map1[s1] = d;
                    }
                }
            }
        }
        now.s = b;
        now.deep = 0;
        q2.push(now);
        if(!q2.empty()){
            now = q2.front();
            q2.pop();
            if(now.deep > 6){
                return ;
            }
            for(int i=0; i<now.s.length(); i++){
                for(int j=0; j<n; j++){
                    string s2 = Change2(now.s, i, j);
                    if(s2 == "")continue;
                    int d = now.deep + 1;
                    if(map1.count(s2)){//在前往后搜过的状态里找到当前的状态，
                                        //说明两中搜索相遇，这时两个步数相加即是答案
                        ans = map1[s2] + d;
                        return ;
                    }
                    else{
                        Node tmp = {s2, d};
                        q2.push(tmp);
                        map2[s2] = d;
                    }
                }
            }
        }
    }
}
int main()
{
    cin >> a >> b;
    while(cin >> A[n] >> B[n]){
        n ++;
    }
    if(a == b){//特判a=b的情况
        cout << 0 << endl;
        return 0;
    }
    bfs();
    if(ans > 10 || ans == 0)cout << "NO ANSWER!\n";
    else cout << ans << endl;
    return 0;
}
```
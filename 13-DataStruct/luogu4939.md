﻿# Agent2        
## 题目描述    
炎炎夏日还没有过去，Agent们没有一个想出去外面搞事情的。每当ENLIGHTENED总部组织活动时，人人都说有空，结果到了活动日，却一个接着一个咕咕咕了。只有不咕鸟Lyn_king一个人冒着太阳等了半个多小时，然后居然看到连ENLIGHTENED行动参谋都咕咕咕了，果然咕咕咕是人类的本性啊。  
作为一个ENLIGHTENED行动指挥，自然不想看到这一点，于是他偷取到了那些经常咕咕咕的Agent的在下来NN天的活动安排表，并且叫上了你来整理。在整理过程中，ENLIGHTENED行动指挥对你说了MM条命令，命令操作如下。  
输入0,a,b，这代表在第aa天到第bb天，有一名Agent要咕咕咕。  
输入1 a，这代表ENLIGHTENED行动指挥询问你根据目前的信息，在第aa天有多少名Agent会咕咕咕。  
作为同是不咕鸟的你，也想要惩戒那些经常咕咕咕的人，所以，请协助完成ENLIGHTENED行动指挥完成整理，并且在他每次询问时，输出正确的答案。  
## 输入格式  
第一行输入两个整数输N,M，下来M行,每行输入一个命令，命令格式见题目描述。    
## 输出格式  
对于每一次询问的操作，都要输出询问的答案。答案之间用换行隔开。       
## 输入样例  
 ```	 
5 5  
0 1 2  
0 1 5  
1 1  
0 3 5  
1 5     
 ```    
## 输出样例  
 ```		
2
2    
 ```   
## 测试网站  	
  [p4939](https://www.luogu.org/problemnew/show/P4939)  	 
## 题目分析  	
这道题思路树状数组+差分。  
差分思想：假设有一个从小到大的数列a[] a 1 3 6 8 9   
然后还有一个差分数组b[] b 1 2 3 2 1   
我们可以看出，这里b[i]=a[i]-a[i-1]，其中令b[1]=a[1]。  
我们知道，树状数组对于单点值的修改十分方便，而对于区间的修改就比较尴尬，这时候就要用到差分。  
还是上面的`a[]`和`b[]`，现在我们使区间`[2,4]`的所有数均`+2`，则`a[]`，`b[]`变为：  
    a 1 5 8 10 9  
    b 1 4 3 2 -1    
事实上，这里只有b[2]和b[5]发生了变化，因为区间内元素均增加了同一个值，所以b[3]，b[4]是不会变化的。  
对于区间[x,y]的修改（增加值为d）在b数组内引起变化的只有 b[x]+=d，b[y+1]-=d。  
而a[i] = b[1]+b[2]+...+b[i]，所以建立在差分数组上的树状数组维护的前缀和是a数组中某个点的值。  
具体实现见代码。    
## 代码示例  
 ```c++	
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
#define N 10000010
int c[N];//C[]为树状数组
int n;
int lowbit(int x)
{
    return x&(-x);
}
void Insert(int i,int x)
{
    while(i<=n)
    {
        c[i]+=x;
        i+=lowbit(i);
    }
}
int getsum(int i)//树状数组求和
{
    int sum=0;
    while(i>0)
    {
        sum+=c[i];
        i-=lowbit(i);
    }
    return sum;
}
int main()
{
    int m, p;
    cin >> n >> m;
    memset(c,0,sizeof(c));
    while(m --)
    {
        cin >> p;
        if(!p)
        {
            int a, b;
            cin >> a >> b;
            Insert(a, 1);//差分，把d[a]+1,同时d[b+1]-1
            Insert(b+1, -1);
        }
        if(p)
        {
            int a;
            cin >> a;
            cout << getsum(a) << endl;
        }
    }
    return 0;
}

```
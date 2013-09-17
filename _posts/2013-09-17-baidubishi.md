---
layout: post

title: "百度笔试题"

categories:
- 算法

tags:
- 算法

---

在看百度2013校园招聘笔试题，发现以下这道题有一定的思考价值。  
**题目**：有20个数组，每个数组有500个元素，并且是有序排列好的，现在在这20*500个数中找出排名前500的数。  

这道题一看就知道是个TopK的问题。解决这个问题思路如下：数组是有序的（假设是升序），我们要利用这个特性。首先构造一个大根堆（大小为数组个数即20），将每个数组当前最大的数放入堆中，然后取出大根堆的根，使用一个统计数组（大小为每个数组的长度即500）保存这个数，将这个数从大根堆中删除，接着再向大根堆中放入刚才删除的那个数的上一个数（数组是有序的），如此反复直到统计数组满了为止。  

{% highlight c++ %}
#include <iostream>
#include<cstdlib>
#include <set>
#include <algorithm>

using namespace std;

const int N = 20;
const int M = 500;
int a[N][M];//二维数组存放测试数据

struct node{
    int *p; //指向数组中末尾
};
struct classcomp{
    bool operator()(const node& left,const node& right)   //降序
    {
        return *left.p>*right.p;
    }
};

typedef multiset<node,classcomp> nodeSet;
typedef multiset<node,classcomp>::iterator setIterator;
int out_put[M];
int out_count=0;

int compare (const void * a, const void * b)
{
  return ( *(int*)a - *(int*)b );
}

void findTopK(int (&a)[N][M],nodeSet& nodeSet)
{
    node nodeArray[N];
    for(int i=0;i<N;i++)
    {
        nodeArray[i].p = &a[i][M-1];//指向每个数组的末尾即每个数组最大的数
    }
    for(int i=0;i<N;i++)
    {
        nodeSet.insert(nodeArray[i]);
    }
    while(out_count<M)
    {
        setIterator iteratorGreatest = nodeSet.begin();
        node n = *iteratorGreatest;//获取大根
        out_put[out_count++] = *n.p;//保存
        nodeSet.erase(iteratorGreatest);//删除
        n.p = n.p--;//指针向前移动
        nodeSet.insert(n);//插入
    }
    for(int i=0;i<M;i++)
    {
        cout<<out_put[i]<<" ";
    }
}

int main()
{
    int i,j;
	//随机生成数据
	for(i=0;i<N;i++)
    {
        for(j=0;j<M;j++)
        {
            a[i][j]=rand()%256;
        }

	}
    //排序
    for(i=0;i<N;i++)
	{
		qsort(&a[i][0],M,sizeof(int),compare);
	}

    nodeSet nodeSet;
    findTopK(a,nodeSet)；
}

{% endhighlight %}


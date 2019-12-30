## 第一章 **算法概述**
算法是指解决问题的一种方法或一个过程。严格的讲，算法是由若干条指令组成的有穷序列

### 算法性质

1. 输入
2. 输出
3. 确定性
4. 有限性
5. 可行性(补充)

### 算法和程序有什么不同

1. 程序是算法用某种程序设计语言的具体实现，而算法可以有多种方式，如自然语言方式、表格方式等。
2. 程序可以不满足有限性，算法必须满足

### 算法复杂度

1. 时间复杂度  
算法的复杂性是算法运行需要的计算机资源的量，需要时间资源的量称为时间复杂性。
2. 空间复杂度  
算法的复杂性是算法运行需要的计算机资源的量，需要空间资源的量称为空间复杂性。
3. 渐进时间复杂度
    - 语句频度 f(n)=算法中基本操作重复执行的次数之和
    - 渐进时间复杂度：随着问题规模n的增长，算法执行时间的增长率和算法的语句频度f(n)的增长率相同，则可记作：T(n)=O(f(n)),称T(n)为算法的(渐进)时间复杂度

## 第二章 **递归与分治**

**分治的主要思想：将一个规模为n的问题分解为K个规模较小的子问题，这些子问题互相独立且与原问题相同，递归地解这些子问题，然后将各子问题的解合并得到原问题的解。**

1. 递归：直接或间接的调用自身的算法称为递归算法
2. 排列算法

    ```
    void Perm(int list[],int k,int m)
    {
        if(k==m)
        {
            for(int i=0;i<=m;i++)
                cout<<list[i];
        }
        else
        {
            for(int i=k;i<=m;i++)
            {
                swap(list[k],list[i]);
                Perm(list,k+1,m);
                swap(list[k],list[i]);
            }
        }
    }

    ```

3. 分治解快速排序

    ```
    int Partition(int a[],int p,int r)
    {
        int i=p,j=r+1;
        int x=a[p];
        //将小于x的元素交换到左边，大于x的区域交换到右边
        while(true)
        {
            while(a[++i]<x&&i<r);
            while(a[--j]>x);
            if(i>=j) break;
            swap(a[i],a[j]);
        }
        a[p]=a[j];
        a[j]=x;
        return j;
    }
    void QuickSort(int a[],int p,int r)
    {
        if(p<r)
        {
            int q=Partition(int a[],int p,int r);
            QuickSort(a,p,q-1);
            QuickSort(a,q+1,r);
        }
    }
    //以下为采用随机策略的快速排序算法
    void RandomizedQuickSort(int a[],int p,int r)
    {
        if(p<r)
        {
            int q=RandomizedPartition(int a[],int p,int r);
            RandomizedQuickSort(a,p,q-1);
            RandomizedQuickSort(a,q+1,r);
        }
    }
    int RandomizedPartition(int a[],int p,int r)
    {
        int i=Random(p,r);
        Swap(a[i],a[p]);
        return Partition(a,p,r);
    }
    ```

4. 找数组a[0:n-1]中的第K小元素，调用RandomizedSelect(a,0,n-1,k)

    ```
    int RandomizedSelect(int a[],int p,int r,int k)
    {
        if(p==r) return a[p];
        int i=RandomizedPartition(a,p,r),j=i-p+1;
        if(k<=j) return RandomizedSelect(a,p,i,k);
        else return RandomizedSelect(a,i+1,r,k-j);
    }
    ```

## 第三章**动态规划策略**

**主要思想：将待求解问题分解成若干子问题，先求解子问题，再结合这些子问题的解得到原问题的解。**
与分治法不同的是，适合用动态规划求解的问题经过分解得到的子问题往往不是互相独立的。  

1. 基本步骤
    - 找出最优解的性质，并刻画其结构特征
    - 递归地定义最优值
    - 以自底向上的方式计算最优值
    - 根据计算最优值得到的信息，构造最优解

2. 0-1背包动态规划解法
    递归方程：  
        ① j<w(i) V(i,j)=V(i-1,j)  
        ② j>=w(i)     V(i,j)=max｛ V(i-1,j)，V(i-1,j-w(i))+v(i) ｝

    ```
    include <iostream>
    using namespace std;
    int w[105], val[105];
    int dp[105][1005];
    int main()
    {
        int t, m, res=-1;
        cin >> t >> m;
        for(int i=1; i<=m; i++)
            cin >> w[i] >> val[i];
        for(int i=1; i<=m; i++) //物品 
            for(int j=t; j>=0; j--) //容量 
            {
                if(j >= w[i])
                    dp[i][j] = max(dp[i-1][j-w[i]]+val[i], dp[i-1][j]);
                else      //只是为了好理解
                    dp[i][j] = dp[i-1][j];
            }
        cout << dp[m][t] << endl;
        return 0;
    }
    ```

    改进后：

    ```
    int f[1010],w[1010],v[1010];//f记录不同承重量背包的总价值，w记录不同物品的重量，v记录不同物品的价值
    int max(int x,int y){//返回x,y的最大值
        if(x>y) return x;
        return y;
    }
    int main(){
    int t,m,i,j;
    memset(f,0,sizeof(f));  //总价值初始化为0
    scanf("%d %d",&t,&m);  //输入背包承重量t、物品的数目m
    for(i=1;i<=m;i++)
        scanf("%d %d",&w[i],&v[i]);  //输入m组物品的重量w[i]和价值v[i]
    for(i=1;i<=m;i++){  //尝试放置每一个物品
        for(j=t;j>=w[i];j--){//倒叙是为了保证每个物品都使用一次
            f[j]=max(f[j-w[i]]+v[i],f[j]);
            //在放入第i个物品前后，检验不同j承重量背包的总价值，如果放入第i个物品后比放入前的价值提高了，则修改j承重量背包的价值，否则不变
        }
    }
    printf("%d",f[t]);  //输出承重量为t的背包的总价
    ```

3. 矩阵连乘法代码,调用Traceback(1,n,s)

    ```
    void MatrixChain(int *p,int n,int **m,int **s)
    {
        for(int i=1;i<=n;i++)
        {
            m[i][j]=0;
        }
        for(int r=2;r<=n;r++)
        {
            for(int i=1;i<n-r+1;i++)
            {
                int j=i+r-1;
                m[i][j]=m[i+1]+p[i-1]*p[i]*p[j];
                s[i][j]=i;
                for(int k=i+1;k<j;k++)
                {
                    int t=m[i][k]+m[k+1][j]+p[i-1]*p[k]*p[j];
                    if(t<m[i][j])
                    {
                        m[i][j]=t;
                        s[i][j]=k;
                    }
                }
            }
        }
    }
    void Traceback(int i,int j,int **s)
    {
        if(i==j)
            return;
        Traceback(i,s[i][j],s);
        Traceback(s[i][j]+1,j,s);
        cout<<"Multiply A"<<i<<", "<<s[i][j];
        cout<<" and A "<<(s[i][j]+1)<<", "<<j<<endl;
    }
    ```

## 第四章**贪心算法**

**主要思想：通过一系列选择来得到问题的解，所做的每个选择都是当前状态下局部最好选择。重点为贪心的标准。**

1. 和动态规划的区别和联系  

    他们都要求问题具有最优子结构的性质。贪心算法并不能对所有问题都得到整体的最优解，但其却更简单更直接，解题效率更高。

2. 背包问题代码贪心标准：按单位重量价值降序排列，从头开始选择物品。

    ```
    void Knapsack(int n,float M,float v[],float w[],float x[])
    {
        Sort(n,v,w);
        int i;
        for(i=1;i<=n;i++)
        {
            x[i]=0;
        }
        float c=M;
        for(i=1;i<=n;i++)
        {
            if(w[i]>c)
            {
                break;
            }
            x[i]=1;
            c -= w[i];
        }
        if(i<=n)
        {
            x[i]=c/w[i];
        }
    }
    ```

3. 最优装载问题(p100)

    ```
    void loading(int x[],int w[],int c,int n)
    {
        int *t=new int[n+1];
        sort(w,t,n);
        for(int i=1;i<=n;i++)
        {
            x[i]=0;
        }
        for(int i=1;i<=n&&w[t[i]]<=c;i++)
        {
            x[t[i]]=1;
            c-=w[t[i]];
        }
    }
    ```

4. 单源最短路径代码：
    ```
    //Dijkstra算法函数，求给定顶点到其余各点的最短路径
    //参数：邻接矩阵、出发点的下标、结果数组、路径前一点记录
    void Dijkstra(int Cost[][N], int v0, int Distance[], int prev[])
    {
        int s[N];
        int mindis,dis;
        int i, j, u;
        //初始化
        for(i=0; i<N; i++)
        {
            Distance[i] = Cost[v0][i];
            s[i] = 0;
            if(Distance[i] == M)
                prev[i] = -1;
            else
                prev[i] = v0;
        }
        Distance[v0] = 0;
        s[v0] = 1; //标记v0
        //在当前还未找到最短路径的顶点中，
        //寻找具有最短距离的顶点
        for(i=1; i < N; i++)
        {//每循环一次，求得一个最短路径
            mindis = M;
            u = v0;
            for (j=0; j < N; j++) //求离出发点最近的顶点
                if(s[j]==0 && Distance[j]<mindis)
                {
                    mindis = Distance [j];
                    u = j;
                } // if语句体结束，j循环结束
            s[u] = 1;
            for(j=0; j<N; j++) //修改递增路径序列（集合）
            if(s[j]==0 && Cost[u][j]<M)
            { //对还未求得最短路径的顶点
                //求出由最近的顶点 直达各顶点的距离
                dis = Distance[u] +Cost[u][j];
                // 如果新的路径更短，就替换掉原路径
                if(Distance[j] > dis)
                {
                    Distance[j] = dis;
                    prev[j] = u;
                }
            } // if 语句体结束，j循环结束
        } // i循环结束
    }
    ```

5. prim和Kruskal算法
    - **prim**:该算法的时间复杂度为O(n2)。与图中边数无关，该算法适合于**稠密图**。
    - **kruskal**:需要对图的边进行访问，所以克鲁斯卡尔算法的时间复杂度只和边有关系，可以证明其时间复杂度为O（eloge）。适合**稀疏图**。
    -  假设一个图G=(V,E)有n个节点，图G的每个节点的出度是一个固定的常数：k。由于E=kV=O(V) ，所以我们把符合E=O(V) 条件的图称为稀疏图。  
    同理 :
    如果一个图G=(V,E)有n个节点，假设图G的每个节点的出度是关于n的一个小数，并且0<f<=1，我们把符合E=fV2(平方)=V2(平方)条件的图称为稠密图。
    比如：一个图节点为16，节点的出度为4，那么f = 0.25。

## 第五章 **回溯法**

***基本思想*：针对所给问题，定义问题的解空间，确定易于搜索的解空间结构，以深度优先方式，从根节点开始搜索解空间，并在搜索过程中用剪枝函数避免无效搜索。回溯法求解问题时，要回溯到根，且根节点的所有子树都已被搜索才结束，但是有时在搜索到问题的一个解时就可以结束了。回溯法适合解组合数较大的问题。**  

1. 解空间树、子集树、排列树

    - 问题的解空间一般用**解空间树**（也称状态空间树）的方式组织，树的根结点位于第1层，表示搜索的初始状态，第2层的结点表示对解向量的第一个分量做出选择后到达的状态，第1层到第2层的边上标出对第一个分量选择结果，依此类推，从树的根结点到叶子结点的路径就构成了解空间的一个可能解。

    - 当所给问题是从n个元素的集合中找出满足某种性质的子集时，相应的解空间树称为**子集树**。

        ```
        void backtrack (int t)  
        {
            if (t>n) output(x);  
            else  
                for (int i=0;i<=1;i++) {  
                    x[t]=i;  
                    if (legal(t)) backtrack(t+1);  
                }  
        }

        ```

    - 当所给问题是确定n个元素满足某种性质的排列时，相应的解空间树称为**排列树**。

        ```
            void backtrack (int t)  
            {
                if (t>n) output(x);  
                else  
                    for (int i=t;i<=n;i++) {  
                        swap(x[t], x[i]);  
                        if (legal(t)) backtrack(t+1);  
                        swap(x[t], x[i]);  
                    }  
            }
        ```

    当所给的问题是从n个元素的集合S中找出满足某种性质的子集时，相应的解空间树成为子集树。
    当所给的问题是确定n个元素满足某种性质的排列时，相应的解空间树称为排列树。排列树通常有n!个叶节点。


## 第六章**分支限界法**

***基本思想*：以广度优先或最小耗费(最大效益)优先的方式搜索问题的解空间树，找出满足约束条件的一个解，或是在满足约束条件的解中找出使某一目标函数值达到极大或极小的解，即在某种意义下的最优解。**  
**常见的有普通队列式分支限界法和优先队列式分支限界法。普通队列式分支限界法将活节点表组织成一个队列，按先进先出的原则选取下一个结点为当前扩展结点，而优先队列式则将活节点表组织成一个优先队列，并按优先队列中规定的结点优先级选取优先级最高的下一个结点成为当前的扩展结点，并一次性产生其所有儿子结点，符合约束条件和剪枝函数的儿子结点成为活节点。**

1. 分支限界法与回溯法区别与联系  
    相同点：二者都是一种在问题的解空间树T上搜索问题解的算法。  
    不同点：  
        1. 在一般情况下，分支限界法与回溯法的求解目标不同。回溯法的求解目标是找出T中满足约束条件的所有解，而分支限界法的求解目标则是找出满足约束条件的一个解，或是在满足约束条件的解中找出使某一目标函数值达到极大或极小的解，即在某种意义下的最优解。  
        2. 回溯法与分支-限界法对解空间的搜索方式不同，回溯法通常采用尝试优先搜索，而分支限界法则通常采用广度优先搜索。  
        3.对节点存储的常用数据结构以及节点存储特性也各不相同，除由搜索方式决定的不同的存储结构外，分支限界法通常需要存储一些额外的信息以利于进一步地展开搜索。


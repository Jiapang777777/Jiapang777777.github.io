---
layout: post
title: Strassen’s algorithm
description: Strassen算法优化报告
keywords: Strassen’s algorithm，优化
categories: 数据结构与算法
---
# 上机实践报告

## 一、目的

1．熟悉算法设计的基本思想

2．掌握 Strassen 算法的基本思想，并且能够分析算法性能

## 二、内容与设计思想

1. 编程实现普通的矩阵乘法；

2. 编程实现 Strassen’s algorithm；

3. 在不同数据规模情况下（数据规模 N=2^3, 2^5, 2^7, 2^9）下，比较两种算法的运行时间各是多少；

4. 修改 Strassen’s algorithm，使之适应矩阵规模 N 不是 2 的幂的情况；

5. 改进后的算法与 2 中的算法在相同数据规模下进行比较。



注意 1：每组样例有对应的执行次数，如果需要得到单次时间，要除以重复数。

注意 2：由于平台的缘故，可能会有 30ms 的波动；解决办法是多提交几次（比如 5 此）取平均数。

## 三、使用环境

推荐使用 C/C++集成编译环境。

## 四、实验过程

1. #### _**写出源代码**_

   ##### 当规模为2的幂的情况

   **普通算法：**

   ```c++
   #include <iostream>
   using namespace std;
   
   void jv(int M)
   {
       int c[M][M];
       int a[513][513];
       int b[513][513];
       //vector<vector<int>> a(m, vector<int>(m));
       //vector<vector<int>> b(m, vector<int>(m));
       int i, j;
       ios::sync_with_stdio(false); //关同步，提高cin / cout效率
       for (i = 0; i < M; i++)
       {
           for (j = 0; j < M; j++)
           {
               cin >> a[i][j];
           }
       }
       for (i = 0; i < M; i++)
       {
           for (j = 0; j < M; j++)
           {
               cin >> b[i][j];
           }
       }
       for (int i = 0; i < M; i++)
       {
           for (int j = 0; j < M; j++)
           {
               c[i][j] = 0;
           }
       }
   
       for (int i = 0; i < M; i++)
       {
           for (int j = 0; j < M; j++)
           {
               for (int k = 0; k < M; k++)
               {
                   c[i][j] = c[i][j] + (a[i][k] * b[k][j]);
               }
           }
       }
   
       for (int i = 0; i < M; i++)
       {
           cout << c[i][0];
           for (int j = 1; j < M; j++)
           {
               cout << " " << c[i][j];
           }
           cout << endl;
       }
   }
   
   int main()
   {
       int M; //数组大小
       int N; //有多少对
       int i, j;
       ios::sync_with_stdio(false); //关同步，提高cin / cout效率
       cin >> N;
       cin >> M;
       for (i = 0; i < N; i++)
       {
           jv(M);
       }
       return 0;
   }
   
   ```

   

   

   **Strassen算法**

   ```c++
   #include <iostream>
   using namespace std;
   
   //矩阵相加
   void Add(int **A, int **B, int **C, int M)
   {
       for (int i = 0; i < M; i++)
       {
           for (int j = 0; j < M; j++)
           {
               C[i][j] = A[i][j] + B[i][j];
           }
       }
   }
   
   //矩阵相减
   void Sub(int **A, int **B, int **C, int M)
   {
       for (int i = 0; i < M; i++)
       {
           for (int j = 0; j < M; j++)
           {
               C[i][j] = A[i][j] - B[i][j];
           }
       }
   }
   
   //普通矩阵的算法
   void normal(int **A, int **B, int **C, int M )
   {
       for ( int i = 0; i < M; i++)
       {
           for ( int j = 0; j < M; j++)
           {
               C[i][j]=0;
               for(int k = 0; k < M; k++)
               C[i][j]+= A[i][k]*B[k][j];
           }
       }
   }
   
   //strassen算法
   int Strassen(int **A, int **B, int **Result, int M)
   {
      if (M <= 32)
       {
          normal(A,B,Result,M);
          return 0;
       }
       
       int NewSize = M / 2;
       /*分块矩阵*/
       int **A11, **A12, **A21, **A22;
       int **B11, **B12, **B21, **B22;
       int **C11, **C12, **C21, **C22;
       int **P1, **P2, **P3, **P4, **P5, **P6, **P7;
       /*存放数组A、B（i、j）的临时变量*/
       int **AResult, **BResult;
   
       A11 = new int *[NewSize];
       A12 = new int *[NewSize];
       A21 = new int *[NewSize];
       A22 = new int *[NewSize];
   
       B11 = new int *[NewSize];
       B12 = new int *[NewSize];
       B21 = new int *[NewSize];
       B22 = new int *[NewSize];
   
       C11 = new int *[NewSize];
       C12 = new int *[NewSize];
       C21 = new int *[NewSize];
       C22 = new int *[NewSize];
   
       P1 = new int *[NewSize];
       P2 = new int *[NewSize];
       P3 = new int *[NewSize];
       P4 = new int *[NewSize];
       P5 = new int *[NewSize];
       P6 = new int *[NewSize];
       P7 = new int *[NewSize];
   
       AResult = new int *[NewSize];
       BResult = new int *[NewSize];
   
       for (int i = 0; i < NewSize; i++)
       {
           A11[i] = new int[NewSize];
           A12[i] = new int[NewSize];
           A21[i] = new int[NewSize];
           A22[i] = new int[NewSize];
   
           B11[i] = new int[NewSize];
           B12[i] = new int[NewSize];
           B21[i] = new int[NewSize];
           B22[i] = new int[NewSize];
   
           C11[i] = new int[NewSize];
           C12[i] = new int[NewSize];
           C21[i] = new int[NewSize];
           C22[i] = new int[NewSize];
   
           P1[i] = new int[NewSize];
           P2[i] = new int[NewSize];
           P3[i] = new int[NewSize];
           P4[i] = new int[NewSize];
           P5[i] = new int[NewSize];
           P6[i] = new int[NewSize];
           P7[i] = new int[NewSize];
   
           AResult[i] = new int[NewSize];
           BResult[i] = new int[NewSize];
       }
   
       //对分块矩阵赋值
       for (int i = 0; i < NewSize; i++)
       {
           for (int j = 0; j < NewSize; j++)
   
           {
               A11[i][j] = A[i][j];
               A12[i][j] = A[i][j + NewSize];
               A21[i][j] = A[i + NewSize][j];
               A22[i][j] = A[i + NewSize][j + NewSize];
   
               B11[i][j] = B[i][j];
               B12[i][j] = B[i][j + NewSize];
               B21[i][j] = B[i + NewSize][j];
               B22[i][j] = B[i + NewSize][j + NewSize];
           }
       }
   
       //计算P1 = A11*(B12-B22)
       Sub(B12, B22, BResult, NewSize);
       Strassen(A11, BResult, P1, NewSize);
   
       //计算P2 = (A11+A12)*B22
       Add(A11, A12, AResult, NewSize);
       Strassen(AResult, B22, P2, NewSize);
   
       //计算P3 = (A21+A22)*B11
       Add(A21, A22, AResult, NewSize);
       Strassen(AResult, B11, P3, NewSize);
   
       //计算P4 = A22*(B21-B11)
       Sub(B21, B11, BResult, NewSize);
       Strassen(A22, BResult, P4, NewSize);
   
       //计算P5 = (A11+A22)*(B11+B22)
       Add(A11, A22, AResult, NewSize);
       Add(B11, B22, BResult, NewSize);
       Strassen(AResult, BResult, P5, NewSize);
   
       //计算P6 = (A12-A22)*(B21+B22)
       Sub(A12, A22, AResult, NewSize);
       Add(B21, B22, BResult, NewSize);
       Strassen(AResult, BResult, P6, NewSize);
   
       //计算P7 = (A11-A21)*(B11+B12)
       Sub(A11, A21, AResult, NewSize);
       Add(B11, B12, BResult, NewSize);
       Strassen(AResult, BResult, P7, NewSize);
   
       //计算C11，C12，C21，C22
       //C11 = P5 + P4 - P2 + P6;
       Add(P5, P4, AResult, NewSize);
       Sub(AResult, P2, BResult, NewSize);
       Add(BResult, P6, C11, NewSize);
   
       //C12=P1+P2
       Add(P1, P2, C12, NewSize);
   
       //C21=P3+P4
       Add(P3, P4, C21, NewSize);
   
       //C22=P5+P1-P3-P7
       Add(P5, P1, C22, NewSize);
       Sub(C22, P3, C22, NewSize);
       Sub(C22, P7, C22, NewSize);
   
       //合并C11，C12，C21，C22
       for (int i = 0; i < NewSize; i++)
       {
           for (int j = 0; j < NewSize; j++)
           {
               Result[i][j] = C11[i][j];
               Result[i][j + NewSize] = C12[i][j];
               Result[i + NewSize][j] = C21[i][j];
               Result[i + NewSize][j + NewSize] = C22[i][j];
           }
       }
   
       //删除数组，回收资源
       for (int i = 0; i < NewSize; i++)
       {
           delete[] A11[i];
           delete[] A12[i];
           delete[] A21[i];
           delete[] A22[i];
           delete[] B11[i];
           delete[] B12[i];
           delete[] B21[i];
           delete[] B22[i];
           delete[] C11[i];
           delete[] C12[i];
           delete[] C21[i];
           delete[] C22[i];
           delete[] P1[i];
           delete[] P2[i];
           delete[] P3[i];
           delete[] P4[i];
           delete[] P5[i];
           delete[] P6[i];
           delete[] P7[i];
           delete[] AResult[i];
           delete[] BResult[i];
       }
       delete[] A11;
       delete[] A12;
       delete[] A21;
       delete[] A22;
       delete[] B11;
       delete[] B12;
       delete[] B21;
       delete[] B22;
       delete[] C11;
       delete[] C12;
       delete[] C21;
       delete[] C22;
       delete[] P1;
       delete[] P2;
       delete[] P3;
       delete[] P4;
       delete[] P5;
       delete[] P6;
       delete[] P7;
       delete[] AResult;
       delete[] BResult;
       return 0;
   }
   
   int main()
   {
       ios::sync_with_stdio(false); //关同步，提高cin / cout效率
       int N, M;
       cin >> N >> M;
       int **A, **B, **C;
       //动态申请二维数组
       A = new int *[M];
       B = new int *[M];
       C = new int *[M];
       for (int i = 0; i < M; i++)
       {
           A[i] = new int[M];
           B[i] = new int[M];
           C[i] = new int[M];
       }
   
       
   
       for (int i = 0; i < N; i++)
       {
           for (int i = 0; i < M; i++)
           { //输入矩阵A
           for (int j = 0; j < M; j++)
           {
               cin >> A[i][j];
           }
           }
      
           for (int i = 0; i < M; i++)
           { //输入矩阵A
           for (int j = 0; j < M; j++)
           {
               cin >> B[i][j];
           }
           }   
           Strassen(A, B, C, M);
   
           for (int i = 0; i < M; i++)
           {
               cout<<C[i][0];
               for (int j = 1; j < M; j++)
               {
                   cout << " " << C[i][j];
               }
               cout << endl;
           }
           
       }
       for (int i = 0; i < M; i++)
           {
               delete[] A[i];
               delete[] B[i];
               delete[] C[i];
           }
           delete[] A;
           delete[] B;
           delete[] C;
       return 0;
   }
   ```

   

   #### **当规模不是N的二次方时**

   普通算法和上面一样

   Strassen算法——只有主函数不同，其他完全一样（所以只贴了主函数代码）

   最简单的想法就是——将矩阵补全为一个2次幂的矩阵，其他地方补为0

   ```c++
   int main()
   {
       ios::sync_with_stdio(false); //关同步，提高cin / cout效率
       int N, M;
       cin >> N >> M;
       int **A, **B, **C;
       //动态申请二维数组
       A = new int *[512];
       B = new int *[512];
       C = new int *[512];
       for (int i = 0; i < 512; i++)
       {
           A[i] = new int[512];
           B[i] = new int[512];
           C[i] = new int[512];
       }
   
       for (int i = 0; i < N; i++)
       {
           for (int i = 0; i < M; i++)
           { //输入矩阵A
               for (int j = 0; j < M; j++)
               {
                   cin >> A[i][j];
               }
           }
   
           for (int i = 0; i < M; i++)
           { //输入矩阵A
               for (int j = 0; j < M; j++)
               {
                   cin >> B[i][j];
               }
           }
   
           //当测试M为非二次幂时，执行以上步骤，通过添加0，将矩阵大小变为二次幂
           int cnt, item;
           cnt = 0;
           item = M;
           while (pow(2, cnt) < M)
           {
               cnt++;
               //cout<<cnt<<endl;
           }
           cnt = pow(2, cnt);
           //cout<<cnt;
           for (int i = item; i < cnt; i++)
           {
               for (int j = 0; j < cnt; j++)
               {
                   A[i][j] = 0;
                   B[i][j] = 0;
               }
           }
           for (int j = item; j < cnt; j++)
           {
               for (int i = 0; i < cnt; i++)
               {
                   A[i][j] = 0;
                   B[i][j] = 0;
               }
           }
   
           Strassen(A, B, C, cnt);
   
           for (int i = 0; i < M; i++)
           {
               cout << C[i][0];
               for (int j = 1; j < M; j++)
               {
                   cout << " " << C[i][j];
               }
               cout << endl;
           }
       }
       for (int i = 0; i < M; i++)
       {
           delete[] A[i];
           delete[] B[i];
           delete[] C[i];
       }
       delete[] A;
       delete[] B;
       delete[] C;
       return 0;
   }
   ```

   

2. _分别画出各个实验结果的折线图_

   ***规模为2的幂时的情况***
   
   ![oom killed pod](/images/blog/截屏2023-04-29 19.21.04.png)

   | 数据规模 | nomal  algorithm (t/ms) | Strassen’s  algorithm (t/ms) |
   | :------: | :---------------------: | :--------------------------: |
   |   2^3    |          0.016          |            0.017             |
   |   2^5    |          0.299          |            0.308             |
   |   2^7    |          6.828          |            6.319             |
   |   2^9    |           195           |            156.25            |

   ------

   这里改进主要是指对Strassen算法的优化
  ![oom killed pod](/images/blog/截屏2023-04-29 19.21.08.png)
   

   | 数据规模 | 改进前 | 改进后 |
   | :------: | :----: | :----: |
   |   2^3    | 0.106  | 0.017  |
   |   2^5    | 1.911  | 0.318  |
   |   2^7    | 30.875 | 6.319  |
   |   2^9    | 493.75 | 156.25 |

   ------

   

   ***当规模不为2的幂时的情况***
   
   ![oom killed pod](/images/blog/截屏2023-04-29 19.21.17.png)


   | 数据规模 | nomal  algorithm (t/ms) | Strassen’s  algorithm (t/ms) |
   | :------: | :---------------------: | :--------------------------: |
   |   2^3    |          0.022          |            0.021             |
   |   2^5    |          0.299          |            0.332             |
   |   2^7    |          6.783          |            6.344             |
   |   250    |         35.125          |            33.75             |
   |   500    |         188.25          |             170              |
   |   2^9    |           195           |            160.75            |

   

## 五、总结

​        先说一下关于strassen算法优化的过程，一开始我递归程序的出口是，当M==1时，直接相乘，这样带来的问题是，时间严重超时，可能是递归了太多次，造成了不必要的开销，所有这些内存分配和复制都会减少算术运算的收益。而且虽然Strassen算法的复杂度较小,但它具有更大的Big-O常数？于是乎，立马开动小脑袋瓜进行优化，优化的方法则是，在strassen所分的矩阵都用普通算法，但这样还是很慢；于是又改成当M小于32时，便用普通算法进行计算，这样就快很多了。

​       所以因为我对strassen算法的设计，在第一个图里可以看出，当规模小于32时，两者运行时间基本一致，但随着规模增大，明显可以看出strassen算法是优于普通算法的。由理论知识可得：普通算法的时间复杂度为O(n^3),而strassen算法约为O(n^2.81)。故实验结果是符合的。

​       当规模不是2的N次幂时，一个很简单的想法就是，把矩阵补全成为2的幂次规模即可。这是因为矩阵的性质可得，就算扩大矩阵（补0），也会保留原有的结果，而添加的那部分为0，最后只输出M*M规模矩阵即可。但这样有一个问题在于，可能会增加很大消耗，我在网上看到了一个优化的办法是，每次递归时将奇数补一行为偶数即可，但我写成这样试了一下，发现并没有很大的提升。

​        关于第三个图的解释：在非2次幂下，strassen算法还是优于普通算法的，而在strassen算法下，规模为512的数据时间是小于500的，我认为是因为当为500时，有一个补充矩阵的操作（但我认为两者应该差不多才对），还可能是因为误差？

建议：感觉这次题目的要求不是很清晰，比如画图那里，改进前后算法比较，不懂这里指的是优化前后，还是改为适合非二次幂前后。（也可能是因为我自己没理解好）

​        

## **六、水杉id**

10205501403刘佳凡

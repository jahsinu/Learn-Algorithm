# 再帰・分割統治法

問題を分解し、部分的な小さな問題を解くことで与えられたもとの問題を解くテクニック。  

## 再帰関数

関数の中で自分自身を呼び出す関数。  
整数nの階乗を求める関数を再帰関数で定義すると以下。  

```cpp
#include <bits/stdc++.h>
using namespace std;

int factorial(int n) {
    if (n == 1) {
        return 1;
    } else {
        return n * factorial(n - 1);
    }
}

int main() {
    cout << factorial(5) << endl;
    // -> 120
    return 0;
}
```

nの階乗は n! = n * (n - 1) * (n - 2) * (n - 3) ... = n * (n - 1)! であり、nが1より大きい場合、部分問題である n - 1の階乗 を含んでいる。  
このため、nを減らして同じ機能の関数(つまり自分自身)を使用して、元の問題の計算に使用できる。  
nが1の時は1を返すように、必ずどこかで関数を終了するように実装しなければいけないことに注意。

## 分割統治法

再帰を用いると、ある問題を2つ以上の小さい問題に分割し再帰関数により部分問題の解を求め、それらの結果を統合することにより元の問題の解を求めることができることがある。  
このようなプログラミング手法を**分割統治法**と呼ぶ。  

* 与えられた問題を分割する (Divide)
* 部分問題を解く (Solve)
* 得られた部分問題の解を統合して、元の問題を解く (Conquer)

配列に含まれる最大値の探索を分割統治法で解く例が以下。  

```cpp
#include <bits/stdc++.h>
using namespace std;

int findMaximum(vector<int>& A, int l, int r) {
    int m = (l + r) / 2;  // Divide
    if (l == r - 1) {
        return A[l];
    }
    int a = findMaximum(A, l, m);  // 前半の部分問題をSolve
    int b = findMaximum(A, m, r);  // 後半の部分問題をSolve
    return max(a, b);              // Conquer
}

int main() {
    vector<int> A{0, 9, 7, 5, 4, 1, 2, 4, 6, 8, 10};
    cout << findMaximum(A, 0, A.size()) << endl;
    // -> 10
    return 0;
}
```

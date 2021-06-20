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

## 全探索

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_A&lang=ja)

### 解答

この問題は対象となる数列の要素数nの最大数が小さいので、全ての組み合わせを判定する全探索を使用できる。  
要素ごとに 選択する/選択しない の2択(2通りの組み合わせ)となり、2通りの組み合わせを網羅する再帰関数の例は以下のような関数になる。  

```cpp
void print_vector(vector<int> S) {
    for (auto it = S.begin(); it != S.end(); it++) {
        cout << *it;
    }
    cout << endl;
    return;
}

// 再帰関数
void rec(vector<int>& S, int i) {
    // 再帰を止める条件
    if(i >= S.size()) {
        print_vector(S);
        return;
    }

    rec(S, i + 1);
    S[i] = 1;       // S[i]を選択する
    rec(S, i + 1);
    S[i] = 0;       // S[i]を選択しない
}

int main() {
    vector<int> S(3);
    for (int i = 0; i < S.size(); i++) {
        S[i] = 0;
    }
    rec(S, 0);
}

// 000
// 001
// 010
// 011
// 100
// 101
// 110
// 111
```

`S`は3要素の配列で、各要素を「選択する(1)」、「選択しない(0)」で示すとする。  
各要素毎に`0` or `1`を選択しながら再帰を繰り返し、`i`が`S.size()`に達した時、`S`が1つの組み合わせを保持していることになる。  
配列要素数を`n`とすると、`0`から`2^n-1`のまでのビット列になる。

同様の考え方で数列`A`の各要素の組み合わせで、求められている`mi`の値を作ることができる組み合わせを見つけ出す。  
`solve(i, mi)`を「`i`番目以降の要素を使って`mi`を作れる場合`true`を返す」関数とすると、以下の2つの部分問題に分割できる。  

* `solve(i, mi)`
  * `solve(i + 1, mi)`  
    → `i`を使わない場合
  * `solve(i + 1, mi - A[i])`  
    → `i`を使う場合

使う要素の値を`mi`から引いていき、`0`になればそれが`mi`を作ることができる組み合わせを表す。

``` cpp
#include <bits/stdc++.h>
using namespace std;

bool solve(vector<int>& A, int i, int mi) {
    if (mi == 0) {
        // miからAの要素の値を引いていき、0になるならばAの要素から
        // miの値を作れることになる
        return true;
    }
    if (i >= A.size()) {
        return false;
    }

    bool res1 = solve(A, i + 1, mi);         // A[i]を使わないケース
    bool res2 = solve(A, i + 1, mi - A[i]);  // A[i]を使うケース
    return (res1 || res2);
}

int main() {
    int n, q;
    cin >> n;
    vector<int> A(n);
    for (int i = 0; i < n; i++) {
        scanf("%d", &A[i]);
    }
    cin >> q;
    for (int i = 0; i < q; i++) {
        int mi;
        scanf("%d", &mi);
        bool res = solve(A, 0, mi);
        cout << (res ? "yes" : "no") << endl;
    }

    return 0;
}
```

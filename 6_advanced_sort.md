# 高等的整列

## マージソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_B&lang=ja)

### 解答

マージソートは分割統治法を使用した高速なアルゴリズム。  
バブルソートなどのO(n^2)の計算量となる初等的アルゴリズムはサイズの大きな配列に対して実用できない。  
マージソートは大きなサイズにも対応できる高等的アルゴリズムの1つ。  

#### マージソート

1. 指定されたn個の要素を含む部分配列を、n/2個の要素を持つ2つの配列に分割する。  
1. その2つの部分配列を、それぞれマージソートする。  
1. 得られた2つのソート済み配列をマージして統合する。

#### 詳細な解説

[https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_5_B&pattern=post&type=general&filter=Algorithm](https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_5_B&pattern=post&type=general&filter=Algorithm)

``` cpp
#include <bits/stdc++.h>
using namespace std;

#define INFTY 0xFFFFFFFF

int cnt;

void merge(vector<unsigned int>& S, int left, int mid, int right) {
    // 左側部分配列を別領域にコピー
    int n1 = mid - left;
    vector<unsigned int> L(n1 + 1);
    copy(S.begin() + left, S.begin() + left + n1, L.begin());
    L[n1] = INFTY;  // ガードとして取り得ない最大値を設定

    // 右側部分配列を別領域にコピー
    int n2 = right - mid;
    vector<unsigned int> R(n2 + 1);
    copy(S.begin() + mid, S.begin() + mid + n2, R.begin());
    R[n2] = INFTY;  // ガードとして取り得ない最大値を設定

    int l_idx = 0;
    int r_idx = 0;
    for (int i = left; i < right; i++) {
        cnt++;
        // 右側配列と左側配列を比較し、小さい方をSに設定
        if (L[l_idx] <= R[r_idx]) {
            // 同値の場合、Lを使用することで安定ソートになる
            S[i] = L[l_idx];
            l_idx++;
        } else {
            S[i] = R[r_idx];
            r_idx++;
        }
    }

    return;
}

void mergeSort(vector<unsigned int>& S, int left, int right) {
    int mid;
    if (left + 1 < right) {
        mid = (left + right) / 2;
        mergeSort(S, left, mid);    // 配列の左側部分配列をマージソート
        mergeSort(S, mid, right);   // 配列の右側部分配列をマージソート
        merge(S, left, mid, right); // ソート済み部分配列をマージ
    }

    return;
}

int main() {
    int n;
    cin >> n;
    vector<unsigned int> S(n);
    for(int i=0; i<n; i++) cin >> S[i];

    mergeSort(S, 0, S.size());

    for(int i = 0; i < S.size(); i++) {
        if(i) cout << " ";
        cout << S[i];
    }
    cout << endl;
    cout << cnt << endl;
    return 0;
}
```
# 高等的整列

## マージソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_B&lang=ja)

### 解答

マージソートは分割統治法を使用した高速なアルゴリズム。  
バブルソートなどのO(n^2)の計算量となる初等的アルゴリズムはサイズの大きな配列に対して実用できない。  
マージソートは大きなサイズにも対応できる高等的アルゴリズムの1つ。  

#### マージソート

1. 指定されたn個の要素を含む部分配列を、n/2個の要素を持つ2つの配列に分割する。(Divide)  
1. その2つの部分配列を、それぞれマージソートする。(Solve)  
1. 得られた2つのソート済み配列をマージして統合する。(Conquer)

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

## パーティション

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_B&lang=ja)

### 解答

パーティションは、数列の最後の要素よりも小さい値と大きな値に分類するアルゴリズム。  
jが配列の先頭pから最後rまで1つずつ移動するので、パーティションの処理はO(n)の計算量となる。  
離れた要素を交換するので注意が必要。

### 詳細な解説

[https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_B&pattern=post&type=general&filter=Algorithm](https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_B&pattern=post&type=general&filter=Algorithm)

```cpp
#include <bits/stdc++.h>
using namespace std;

int partition(vector<int>& A, int p, int r) {
    // A[r]を基準値xとする
    int x = A[r];
    // iはx以下の値を持つ数列の最後のidxとなる
    int i = p - 1;
    // 配列要素を1つずつチェック
    for (int j = p; j < r; j++) {
        if (A[j] <= x) {        // x以下の値があったら
            i++;                // x以下の値が増えるのでiを進める
            swap(A[i], A[j]);   // A[i]にx以下の値が来るよう、入れ替える
        }
    }
    swap(A[i + 1], A[r]);       // 最後にA[r]をA[i]の位置に入れ替える
    return i + 1;               // 基準値の現在のidxを返す
}

int main() {
    int n;
    cin >> n;
    vector<int> A(n);
    for (int i = 0; i < n; i++) cin >> A[i];

    int pos = partition(A, 0, A.size() - 1);

    for (int i = 0; i < A.size(); i++) {
        if (i) cout << " ";
        if (i == pos) {
            cout << "[" << A[i] << "]";
        } else {
            cout << A[i];
        }
    }
    cout << endl;

    return 0;
}
```

## クイックソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_C&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_C&lang=ja)

### 解答

クイックソートはパーティションと分割統治法を使用した高速なアルゴリズム。  
分割してパーティションを行った際に元の配列内でソートが完了するので、マージソートと異なり、統治にあたる明示的な処理がない。  

#### クイックソート

1. パーティションにより対象の部分配列を前後2つの部分配列とする(Divide)
1. 前方配列にquickSortを行う(Solve)
1. 後方配列にquickSortを行う(Solve)

#### 詳細な解説

[https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_C&pattern=post&type=general&filter=Algorithm](https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_C&pattern=post&type=general&filter=Algorithm)

```cpp
#include <bits/stdc++.h>
using namespace std;

#define INFTY 0xFFFFFFFF

struct Card {
    char suit;
    unsigned int value;
};

void merge(vector<Card>& S, int left, int mid, int right) {
    int n1 = mid - left;
    int n2 = right - mid;
    vector<Card> L(n1 + 1);
    vector<Card> R(n2 + 1);

    copy(S.begin() + left, S.begin() + left + n1, L.begin());
    copy(S.begin() + mid, S.begin() + mid + n2, R.begin());
    L[n1].value = INFTY;
    R[n2].value = INFTY;

    int l_idx = 0;
    int r_idx = 0;
    for (int i = left; i < right; i++) {
        if (L[l_idx].value <= R[r_idx].value) {
            S[i] = L[l_idx];
            l_idx++;
        } else {
            S[i] = R[r_idx];
            r_idx++;
        }
    }

    return;
}

void mergeSort(vector<Card>& S, int left, int right) {
    int mid;
    if (left + 1 < right) {
        mid = (left + right) / 2;
        mergeSort(S, left, mid);
        mergeSort(S, mid, right);
        merge(S, left, mid, right);
    }

    return;
}
int partition(vector<Card>& A, int p, int r) {
    Card x = A[r];
    int i = p - 1;
    for (int j = p; j < r; j++) {
        if (A[j].value <= x.value) {
            i++;
            swap(A[i], A[j]);
        }
    }
    swap(A[i + 1], A[r]);
    return i + 1;
}

void quickSort(vector<Card>& A, int p, int r) {
    if (p < r) {
        int q = partition(A, p, r);
        quickSort(A, p, q - 1);
        quickSort(A, q + 1, r);
    }
}

int main() {
    int n;
    cin >> n;
    vector<Card> A(n);
    vector<Card> B(n);

    for (int i = 0; i < n; i++) {
        char suit[4];
        int value;
        scanf("%s %d", suit, &value);
        A[i].suit = B[i].suit = suit[0];
        A[i].value = B[i].value = value;
    }

    // マージソート (安定なソート)
    mergeSort(A, 0, A.size());
    // クイックソート (安定でないソート)
    quickSort(B, 0, B.size() - 1);

    // マージソートの結果と比較し、安定な結果かチェック
    bool isStable = true;
    for (int i = 0; i < n; i++) {
        if (A[i].suit != B[i].suit || A[i].value != B[i].value) {
            isStable = false;
            break;
        }
    }
    cout << (isStable ? "Stable" : "Not stable") << endl;

    for (int i = 0; i < B.size(); i++) {
        cout << B[i].suit << " " << B[i].value << endl;
    }
    return 0;
}
```

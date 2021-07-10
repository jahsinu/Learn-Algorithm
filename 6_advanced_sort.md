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

パーティションは、ある基準値よりも小さい値と大きな値に分類するアルゴリズム。  
jが配列の先頭pから最後rまで1つずつ移動するので、パーティションの処理はO(n)の計算量となる。  
離れた要素を交換するので注意が必要。

この問題では、配列の最終要素の値が基準値となる。

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

クイックソートはパーティションによりバランス良く半分ずつに配列を分割できればO(nlogn)の計算量となり、最も高速なソートアルゴリズム。  
しかしパーティションの基準の選択には工夫が必要で、以下の例のように一定の(配列の最終要素)基準を使用した場合、データの並びによっては効率が悪くなり、最悪O(n^2)の計算量となる。  
再帰の階層が深くなり過ぎ、スタックオーバーフローが発生する可能性もある。  
基準をランダムに選ぶ、配列から適当な値を選びその中央値を選ぶなどの工夫が行われる。

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

## 計数ソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_6_A&lang=ja)

### 解答

以下の詳細な解説のページがわかりやすい。  
記載されているように、数値の最大値分のサイズを持つ数列Cが必要となるため、サイズに比例するメモリと計算量(O(n+k))が必要になる。  

### 詳細な解説

[https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_A&pattern=post&type=general&filter=Algorithm](https://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=ALDS1_6_A&pattern=post&type=general&filter=Algorithm)

```cpp
#include <bits/stdc++.h>
using namespace std;

#define VMAX 10000

void CountingSort(vector<unsigned int>& A, vector<unsigned int>& B) {
    /* Cのindexが最大値である10000を表現できなくてはいけないので、
       VMAX + 1の要素数が必要 */
    vector<unsigned int> C(VMAX + 1);
    for (int i = 0; i < A.size(); i++) {
        C[0] = 0;
    }

    /* C[i]にiの出現回数を記録する */
    for (int i = 0; i < A.size(); i++) {
        C[A[i]]++;
    }

    /* C[i]にi以下の数の出現数を記録する */
    for (int i = 1; i < C.size(); i++) {
        C[i] = C[i] + C[i - 1];
    }

    /* 数列Aの後ろからチェックすることで安定なソートになる */
    for (int i = A.size() - 1; i >= 0; i--) {
        B[C[A[i]] - 1] = A[i];
        C[A[i]]--;
    }

    return;
}

int main() {
    int n;
    cin >> n;
    vector<unsigned int> A(n);
    for(int i = 0; i < A.size(); i++) {
        scanf("%u", &A[i]);
    }

    vector<unsigned int> B(n);
    CountingSort(A, B);

    for(int i = 0; i < B.size(); i++) {
        if(i) cout << " ";
        cout << B[i];
    }
    cout << endl;
    
    return 0;
}
```

## 標準ライブラリによる整列

STLでは配列やコンテナの要素に対する整列を行うsort関数が提供されている。

### sort

`sort()`はクイックソートをベースとする高速なソート(O(nlogn))。  
最悪ケースで計算量がO(n^2)になる弱点も対策されている。  
ただし安定なソートではないことに注意。

安定したソートを行う場合は`stable_sort()`を使用する。  
`stable_sort()`はマージソートがベース。  
計算量はO(nlogn)だが、`sort()`より速度に劣り、よりメモリを必要とする。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    vector<int> v;
    int a[5];

    cin >> n;
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        v.push_back(x);
        a[i] = x;
    }

    // vctorの場合、先頭イテレータ、末尾イテレータを指定
    sort(v.begin(), v.end());
    // 配列の場合、先頭ポインタ、末尾ポインタを指定
    sort(a, a + n);

    cout << "vectorの場合: ";
    for (int i = 0; i < v.size(); i++) {
        if (i) cout << " ";
        cout << v[i];
    }
    cout << endl;

    cout << "配列の場合:   ";
    for (int i = 0; i < n; i++) {
        if (i) cout << " ";
        cout << a[i];
    }
    cout << endl;

    return 0;
}
```
```sh
# 入力
5
5 3 4 1 2
# 出力
vectorの場合: 1 2 3 4 5
配列の場合:   1 2 3 4 5
```

## 反転数

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_D&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_5_D&lang=ja)

### 解答

この問題は本当にバブルソートで行おうとすると時間内に出力を得ることができない。  

実際に反転した際にカウントしていく方法をとることができないので、別の反転数の数え方を使用する。  
例えば以下の数列Aをソートする場合、`A[0]` から `A[idx - 1]` の範囲にある `A[idx]` より大きい値の数を `C[idx]` とすると、`C[idx]` の総和 `10` が数列Aの反転数となる。

| idx   | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| A     | 5 | 3 | 6 | 2 | 1 | 4 |
| C     | 0 | 1 | 0 | 3 | 4 | 2 |

この方法をマージソートに応用する。  

以下のマージソート実装の `merge()` では、`L[l_idx]` より小さな `R[r_idx]` を `S[i]` に代入した際に、`L` に残っている要素数 `n1 - l_idx` が反転数となる。  
各再帰処理内で反転数を計算し、各反転数を加算していく。

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long llong;
#define INFTY 0xFFFFFFFF

llong merge(vector<unsigned int>& S, int left, int mid, int right) {
    int n1 = mid - left;
    vector<unsigned int> L(n1 + 1);
    copy(S.begin() + left, S.begin() + left + n1, L.begin());
    L[n1] = INFTY;

    int n2 = right - mid;
    vector<unsigned int> R(n2 + 1);
    copy(S.begin() + mid, S.begin() + mid + n2, R.begin());
    R[n2] = INFTY;

    llong cnt = 0;      // 反転数
    int l_idx = 0;
    int r_idx = 0;
    for (int i = left; i < right; i++) {
        // 右側配列と左側配列を比較し、小さい方をSに設定
        if (L[l_idx] <= R[r_idx]) {
            S[i] = L[l_idx];
            l_idx++;
        } else {
            S[i] = R[r_idx];
            r_idx++;
            cnt += n1 - l_idx;      // 反転数を算出
        }
    }

    return cnt;
}

llong mergeSort(vector<unsigned int>& S, int left, int right) {
    int mid;
    llong v1, v2, v3;
    if (left + 1 < right) {
        mid = (left + right) / 2;
        v1 = mergeSort(S, left, mid);
        v2 = mergeSort(S, mid, right);
        v3 = merge(S, left, mid, right);
        return v1 + v2 + v3;
    }

    return 0;
}

int main() {
    int n;
    cin >> n;
    vector<unsigned int> S(n);
    for(int i=0; i<n; i++) cin >> S[i];

    llong ans = mergeSort(S, 0, S.size());
    cout << ans << endl;
    return 0;
}
```

## 最小コストソート

[TODO]

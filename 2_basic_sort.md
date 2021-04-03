---
title: 2. 初等的整列
tags: []
---

# 初等的整列

## 挿入ソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_1_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_1_A&lang=ja)

### 回答

問題で示されたアルゴリズムをC++で実装し、標準入力からのデータ取得と画面出力処理を追加すると  
以下になる。  
配列ではなくvectorを使用し、ソート関数の引数は参照渡しとしている(データ数が多くなることを考慮)。

```cpp
#include <bits/stdc++.h>
using namespace std;

void trace(vector<int> &A) {
    for (int i = 0; i < A.size(); i++) {
        if (i > 0) cout << " ";
        cout << A[i];
    }
    cout << endl;
    return;
}

void insertionSort(vector<int> &A) {
    trace(A);
    // idx0はソート済みと考え、idx1を最初のソート対象とする
    for (int i = 1; i < A.size(); i++) {
        // ソート対象を退避
        int v = A[i];
        // 比較対象の初期idx
        int j = i - 1;
        while ((j >= 0) && (A[j] > v)) {
            // ソート対象より大きな値をずらしていく
            A[j + 1] = A[j];
            j--;
        }
        // ソート対象を空いた位置に配置する
        A[j + 1] = v;
        trace(A);
    }
    return;
}

int main() {
    int N;
    cin >> N;

    vector<int> A(N);
    for (int i = 0; i < N; i++) {
        cin >> A[i];
    }

    insertionSort(A);
    return 0;
}
```

### 挿入ソートの計算量

挿入ソートでは各iのループで、最悪のケースではi回の入れ替え処理が発生する。  
データの並びとデータ数によって計算量が多くなる。  
初期データが降順に並んていると計算量が $O(N^2)$ になるので注意が必要。

## バブルソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_A&lang=ja)

### 回答

問題で示されたアルゴリズムをC++で実装し、標準入力からのデータ取得と画面出力処理を追加すると  
以下になる。  

問題で示されたアルゴリズムでは、内側のループが必ずidx1まで回る。
外側のループが回るたびにソート済み要素が増えていくので、外側ループが回る度に内側ループの  
ループ上限を減らしていく。

```cpp
#include <bits/stdc++.h>
using namespace std;

int bubbleSort(vector<int> &A) {
    int swapCnt = 0;    // 入れ替えカウント
    bool flag = true;   // ソート継続フラグ
    // flagが立っている間ループを回す
    // ループ毎にソート済み要素が増えていくのでソート済み
    // indexをインクリメントしていく
    for(int i = 0; flag; i++) {
        flag = false;
        for (int j = A.size() - 1; j > i; j--) {
            if (A[j] < A[j - 1]) {
                swap(A[j], A[j - 1]);
                swapCnt++;
                flag = true;
            }
        }
    }
    return swapCnt;
}

int main() {
    int N;
    cin >> N;

    vector<int> A(N);
    for (int i = 0; i < N; i++) {
        cin >> A[i];
    }

    int swapCnt = bubbleSort(A);

    for (int i = 0; i < A.size(); i++) {
        if (i > 0) cout << " ";
        cout << A[i];
    }
    cout << endl;
    cout << swapCnt << endl;
    return 0;
}
```

### バブルソートの計算量

データ個数を$N$とすると、未ソート部分列の隣同士の比較を $N-1回、N-2回…、1回$ で  
合計$(N^2 - N) / 2$ 回行われる(最悪ケース)。  
計算量オーダーでは $O(N^2)$ になるので注意が必要。

## 選択ソート

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_B&lang=ja)

### 回答

問題で示されたアルゴリズムをC++で実装し、標準入力からのデータ取得と画面出力処理を追加すると  
以下になる。  

```cpp
#include <bits/stdc++.h>
using namespace std;

int selectionSort(vector<int> &A) {
    int swapCnt = 0;
    // 検索範囲内の最小値を探し、入れ替えを行う
    // ループが進む度に検索範囲は狭くなっていく
    for (int i = 0; i < A.size(); i++) {
        int minj = i; // 最小値のインデックスを保持する
        // 検索範囲内の最小値をA[i]と入れ替える
        for (int j = i; j < A.size(); j++) {
            // より小さい値のidxでminjを更新していく
            if (A[j] < A[minj]) {
                minj = j;
            }
        }
        if (A[i] != A[minj]) {
            swap(A[i], A[minj]);
            swapCnt++;
        }
    }
    return swapCnt;
}

int main() {
    int N;
    cin >> N;

    vector<int> A(N);
    for (int i = 0; i < N; i++) {
        cin >> A[i];
    }

    int swapCnt = selectionSort(A);

    for (int i = 0; i < A.size(); i++) {
        if (i > 0) {
            cout << " ";
        }
        cout << A[i];
    }
    cout << endl;
    cout << swapCnt << endl;
    return 0;
}
```

### 選択ソートの計算量

データ個数を$N$とすると、未ソート部分列の最小値を見つけるための比較を $N-1回、N-2回…、1回$ で  
合計$(N^2 - N) / 2$ 回行われる(常に)。  
計算量オーダーでは $O(N^2)$ になるので注意が必要。

## 安定なソート

キーの値が同じ要素を2つ以上含むデータをソートした場合に、処理の前後でそれらのデータの順番が入れ替わらないソートアルゴリズムを安定なソートと呼ぶ。

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_C&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_C&lang=ja)

### 回答

バブルソートは安定なソートアルゴリズムなので常に"Stable"。  
選択ソートは安定ではないソートアルゴリズムなので、バブルソートの結果と比較して並びが異なっていれば"Not stable"とする。

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Card {
    char suit;
    int value;
};

void BubbleSort(vector<Card>& C) {
    for(int i = 0; i < C.size(); i++) {
        for(int j = C.size() - 1; j > i; j--) {
            if(C[j].value < C[j - 1].value) swap(C[j], C[j - 1]);
        }
    }
    return;
}

void SelectionSort(vector<Card>& C) {
    for(int i = 0; i < C.size(); i++) {
        int minj = i;
        for(int j = i; j < C.size(); j++) {
            if(C[j].value < C[minj].value) minj = j;
        }
        swap(C[i], C[minj]);
    }
    return;
}

void print(vector<Card>& C) {
    for(int i = 0; i < C.size(); i++) {
        if(i > 0) cout << " ";
        cout << C[i].suit << C[i].value;
    }
    cout << endl;
}

bool isStable(vector<Card>& C1, vector<Card>& C2) {
    for(int i = 0; i < C1.size(); i++) {
        if(C1[i].suit != C2[i].suit) return false;
    }
    return true;
}

int main()
{
    int N;
    cin >> N;

    vector<Card> C1(N);
    vector<Card> C2(N);
    for (int i = 0; i < N; i++) {
        cin >> C1[i].suit >> C1[i].value;
    }
    // C1をC2にコピー
    C2 = C1;

    BubbleSort(C1);
    print(C1);
    // BubbleSortは安定なソートなので常に"Stable"出力
    cout << "Stable" << endl;

    SelectionSort(C2);
    print(C2);
    if(isStable(C1, C2)) cout << "Stable" << endl;
    else cout << "Not stable" << endl;

    return 0;
}
```

## シェルソート

シェルソートは、ほぼ整列されたデータに対しては高速に動作する挿入ソートの特徴を活かすアルゴリズム。

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_D&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_2_D&lang=ja)

### 回答

数列Gは基本的には最終的に1になる減少数列であれば良いっぽい。  
ただし $g_{n+1} = 3g_n + 1$ の数列を用いると計算量が $O^{1.25}$ になると予測されているのだそう。

```cpp
#include <bits/stdc++.h>
using namespace std;

unsigned int insertionSort(vector<int> &A, int g) {
    unsigned int cnt = 0;
    for (int i = g; i < A.size(); i++) {
        int v = A[i];
        int j = i - g;
        while (j >= 0 && A[j] > v) {
            A[j + g] = A[j];
            j = j - g;
            cnt++;
        }
        A[j + g] = v;
    }
    return cnt;
}

unsigned int shellSort(vector<int> &A, int n, vector<int> &G) {
    unsigned int cnt = 0;
    // 数列 G  = {1, 4, 13, 40, 121, 364, 1093, ...} を生成
    for (int h = 1;;) {
        if (h > n) break;
        G.push_back(h);
        h = 3 * h + 1;
    }

    for (int i = G.size() - 1; i >= 0; i--) {
        cnt += insertionSort(A, G[i]);
    }
    return cnt;
}

int main() {
    int n;
    cin >> n;

    vector<int> A(n);
    for (int i = 0; i < n; i++) scanf("%d", &A[i]);

    vector<int> G;
    unsigned int cnt = shellSort(A, n, G);

    //出力
    // 1行目に整数m
    cout << G.size() << endl;
    // 2行目にm個の整数Gi(i=0,1,...,m−1)を空白区切り
    for (int i = G.size() - 1; i >= 0; i--) {
        if (i < G.size() - 1) cout << " ";
        cout << G[i];
    }
    cout << endl;
    // 3行目に、Gを用いた場合のプログラムが終了した直後のcntの値
    cout << cnt << endl;
    // 続くn行に整列したAi(i=0,1,...,n−1)を出力
    for (int i = 0; i < n; i++) {
        cout << A[i] << endl;
    }
    return 0;
}
```

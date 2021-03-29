# 探索

探索とはデータの集合の中から与えられたキーの位置や存在の有無を調べる問題。  
基本的な探索アルゴリズムは以下。

- 線形探索  
  配列の先頭から順番に調べていく方法。  
  効率は悪いが、データの並び方に関係なく適用可能。  

- 二分探索  
  データが整列されていることを前提とする方法。  
  1. 配列の中央の値を調べる
  2. キーに一致すれば終了
  3. キーが中央の値より小さければ、中央より左側を次の対象範囲に  
     中央の値より大きければ、中央より右側を次の対象範囲として1.に戻る  

  計算ステップ毎に検索範囲が狭くなっていくので高速に探索できる。

- ハッシュ法  
  ハッシュ関数と呼ばれる関数で算出した値をによって、要素の格納場所を決定する  
  ハッシュテーブルを用いるアルゴリズム。  
  (最初からハッシュテーブルでデータ管理を行う必要がある)  
  キーを引数としてハッシュ関数を呼び出すことでデータ位置を特定できるので  
  高速な探索が可能となる。

## 線形探索

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_A&lang=ja)

```cpp
#include <bits/stdc++.h>
using namespace std;

constexpr int NOT_FOUND = 0xFFFFFFFF;

// 線形探索関数
int linear_search(vector<int> &S, int key) {
    // この問題ではkeyがSに含まれるか否かだけがわかれば良く、
    // Sに含まれる総数を数える問題ではない。  
    // このためkeyに一致する要素があれば、即探索ループを抜けて良い。

    S[S.size() - 1] = key; // ガードを追加
    int i = 0;
    while (S[i] != key) i++;    // ガードがあるのでループ制御の判定が不要
    if (i < S.size() - 1) return i;
    return NOT_FOUND;

    /*
    for (int i = 0; i < S.size(); i++) {
        if (S[i] == key) return i;
    }
    return NOT_FOUND;
    */
}

int main() {
    int n;
    cin >> n;
    vector<int> S(n + 1); // ガードを追加する余裕を持って確保する
    for (int i = 0; i < n; i++) cin >> S[i];

    int count = 0;
    int q;
    cin >> q;
    for (int i = 0; i < q; i++) {
        int key;
        cin >> key;
        int result = linear_search(S, key);
        if (result != NOT_FOUND) {
            count++;
        }
    }
    cout << count << endl;
    
    return 0;
}
```

探索処理実施時に、配列の末尾にkeyの値を追加しておく。  
こうすることでループ終了判定 `for (int i = 0; i < S.size(); i++)` とキー一致判定 `if (S[i] == key)` のうちループ終了判定が不要になり、 `while (S[i] != key)` で済むようになる。  
最後にNOT_FOUNDの判定のため、indexが最終要素に達しているか判定する必要があるが、ループ毎に判定しなくても良くなる。

## 二分探索

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_B&lang=ja)

問題内容は線形探索と同じ内容だが、数列Sの最大要素数が10倍、Tの最大要素数が100倍になっている。  
このため線形探索では時間オーバーになることが予想される。  

> Sの要素は昇順に整列されている  

という制約を活かし、二分探索での探索を行う。

### 回答

```cpp
#include <bits/stdc++.h>
using namespace std;

constexpr int NOT_FOUND = 0xFFFFFFFF;

int binary_search(vector<int>& S, int key) {
    int left = 0;                       // 左端インデックス
    int right = S.size();               // 右端インデックス
    while (left < right) {
        int mid = (left + right) / 2;   // 中間インデックス
        if (S[mid] == key) {
            return mid;                 // midがkey一致すれば返却
        } else if (key < S[mid]) {
            right = mid;                // 半分より左側を次の検索対象に
        } else {
            left = mid + 1;             // 半分より右側を次の検索対象に
        }
    }
    return NOT_FOUND;
}

int main() {
    int n;
    cin >> n;
    vector<int> S(n);
    for (int i = 0; i < n; i++) cin >> S[i];

    int count = 0;
    int q;
    cin >> q;
    for (int i = 0; i < q; i++) {
        int key;
        cin >> key;
        int result = binary_search(S, key);
        if (result != NOT_FOUND) {
            count++;
        }
    }
    cout << count << endl;
    
    return 0;
}
```
以下の表は要素数nの配列に対する線形探索と二分探索の比較演算の回数(最悪ケース)。

|要素数(n)|線形探索|二分探索|
|--|--|--|
|100|100回|7回|
|10000|10000回|14回|
|1000000|1000000回|20回|

この問題は入力数列が昇順で整列されているが、それ以外のケースでも探索対象をソートすれば二分探索を使用可能となる。  
大きなデータでは初等整列で学んだソートアルゴリズムでは性能が良くないため、もっと高等的なソートアルゴリズムを使用する必要がある。  
(高等整列は別途まとめる)


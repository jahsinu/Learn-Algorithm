---
title: 1. アルゴリズムと計算量
tags: []
---

# アルゴリズムと計算量

## 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_1_D&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_1_D&lang=ja)

## 悪い解答例

以下は悪い例。  
この問題では最大で20万件のデータが与えられる。  
総当りでループを繰り返すと制限時間を超えてしまう。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // データの数を取得
    int n = 0;
    cin >> n;

    // データを取得
    vector<int> R(n);
    for (int i = 0; i < n; i++) {
        cin >> R[i];
    }

    int max_val = -1000000000;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            int result = R[j] - R[i];
            max_val = max(max_val, result);
        }
    }
    cout << max_result << endl;
    return 0;
}
```

## 計算量を考慮した解答例

一番利益が出るケースの利益を出すので、特定時点の価格の以前の最安値が重要。  
以下のようにデータを読み込みながら最安価格と最高利益を更新していくと、  
ループが1つになり、配列も不要となる。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // データの数を取得
    int n = 0;
    cin >> n;

    int val, min_val;
    int max_result = -1000000000;
    for (int i = 0; i < n; i++) {
        cin >> val;
        if(i == 0) {
            // 最安価格の初期値とする
            min_val = val;
            continue;
        }
        // 利益を算出し、最大利益を更新
        int result = val - min_val;
        max_result = max(max_result, result);
        // 最安価格を更新
        min_val = min(min_val, val);
    }

    cout << max_result << endl;
    return 0;
}
```

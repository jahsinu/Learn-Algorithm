---
title: 3. データ構造
tags: []
---

# データ構造

## データ構造とは

データ構造は以下の3つの概念で成り立っている。

- (データの)集合  
  配列や構造体などの基本データ構造でデータの集合を保持
- 規則  
  集合を正しく操作/管理/保持するための一定のルール
- 操作  
  「要素の挿入」や「要素の取り出し」などデータの集合に対する操作。  
  「要素数を調べる」、「集合が空かどうか調べる」という問い合わせも含まれる。

### 基本的なデータ構造

- スタック  
  一時的なデータ退避に有効なデータ構造。  
  最後に入ったデータが最初に取り出される(LIFO: Last In First Out)。
- キュー  
  データの到着順に処理際に有効なデータ構造。
  最初に入ったデータが最初に取り出される(FIFO: First In First Out)。
- リスト  
  順序を保ちつつ特定位置へのデータ追加、削除が可能なデータ構造。

## スタック

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_A&lang=ja)

### 回答

スタックをClassで実装してみると以下のような感じ。  
本来はスタックが空かどうかの確認(isEmpty())や、スタックが満杯かどうかの確認(isFull())も実装し、pop()やpush()時にエラー処理を行うべき。

配列Sのidx0は使用しない。  
topはスタック内の最終要素のidxであり、スタック内の要素数でもある。  
topが0の場合はスタックが空であることを示す。

```cpp
#include <bits/stdc++.h>
using namespace std;

constexpr int MAX_NUM_ELEM = 128;

class opeStack {
private:
    int S[MAX_NUM_ELEM];
    int top;

public:
    opeStack() : top(0) { }
    int push(int x) {
        // topをインクリメントしてxを設定
        S[++top] = x;
        return top;
    }
    int pop() {
        // topが指している値を返却し、デクリメント
        return S[top--];
    }
};

int main() {
    opeStack S;
    int a, b;
    char s[10];
    while (scanf("%s", s) != EOF) {
        if (s[0] == '+') {
            b = S.pop();
            a = S.pop();
            S.push(a + b);
        } else if (s[0] == '-') {
            b = S.pop();
            a = S.pop();
            S.push(a - b);
        } else if (s[0] == '*') {
            b = S.pop();
            a = S.pop();
            S.push(a * b);
        } else {
            S.push(atoi(s));
        }
    }

    cout << S.pop() << endl;
    return 0;
}
```

## キュー

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_B&lang=ja)

### 回答

キューをClassで実装したのが以下。  
キューの先頭が常にidx0になるようにすると、デキューの度に全要素の移動が発生し、計算量が多くなる。  
そのため先頭位置、終端位置を管理し、エンキュー/デキューの度にインデックスを移動させる。  
そのままではそのうちインデックスが配列範囲外になってしまうので、インデックスが配列範囲を超えたら、idx0へのリセットが必要(リングバッファとする)。  

```cpp
#include <bits/stdc++.h>
constexpr int QUEUE_MAX = 100008;
using namespace std;

struct proc {
    char name[16];  // プロセス名
    int time;       // 必要時間
};

class rr_queue {
private:
    proc proc_array[QUEUE_MAX];     // データを格納する配列
    int head;                       // 先頭位置を示すインデックス
    int tail;                       // 終端位置を示すインデックス(enqueueする位置)
    int count;                      // 要素数

public:
    rr_queue() : head(0), tail(0), count(0) {}
    int enqueue(proc elem) {
        if (isFull()) throw logic_error("Queue is FULL.");  // エラー
        proc_array[tail++] = elem;  // 要素追加してtailをインクリメント
        tail %= QUEUE_MAX;          // インデックスが範囲外にならないようラップアラウンド
        count++;                    // 要素数をインクリメント
        return count;
    }
    int dequeue(proc *result) {
        if (isEmpty()) throw logic_error("Queue is EMPTY.");  // エラー
        *result = proc_array[head++];   // 要素を取り出してheadをインクリメント
        head %= QUEUE_MAX;              // インデックスが範囲外にならないようラップアラウンド
        count--;                        // 要素数をデクリメント
        return count;
    }
    bool isEmpty() {
        return count ? false : true;    // 要素数が0ならtrue返却
    }
    bool isFull() {
        return count >= QUEUE_MAX;      // 要素数が最大範囲を超えていたらtrue返却
    }
};

int main() {
    int n, q;
    cin >> n >> q;

    // プロセス情報読み込み
    rr_queue Q;
    proc elem;
    for (int i = 0; i < n; i++) {
        scanf("%s", elem.name);
        scanf("%d", &elem.time);
        try {
            Q.enqueue(elem);
        } catch (logic_error e) {
            cerr << "Exception : " << e.what() << endl;
            return -1;
        }
    }

    // ラウンドロビン シミュレーション
    int elapse = 0;
    while (!Q.isEmpty()) {              // キューが空になるまでループ
        try{
            Q.dequeue(&elem);
            int t = min(q, elem.time);      // 処理時間を取得(最大でもq)
            elem.time -= t;                 // 必要時間を減算
            elapse += t;                    // 経過時間追加
            if (elem.time) {
                Q.enqueue(elem);            // 必要時間が残っていればエンキュー
            } else {
                // 必要時間が0になったらプロセス名、経過時間を出力
                cout << elem.name << " " << elapse << endl;
            }
        } catch (logic_error e) {
            cerr << "Exception : " << e.what() << endl;
            return -1;
        }
    }

    return 0;
}
```
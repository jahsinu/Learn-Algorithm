---
title: 3. データ構造
tags: []
---

<script type="text/javascript" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">
 MathJax.Hub.Config({
 tex2jax: {
 inlineMath: [['$', '$'] ],
 displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
 }
 });
</script>

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

### 解答

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

### 解答

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

## 双方向連結リスト

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_C&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_C&lang=ja)

### 解答

連結リストをClassで実装したのが以下。  
連結リストの先頭データは有効な値を持たないガードノードとする(下記ソース内の`nil`)。  
ガードノードを用意することで、先頭、終端へのノード追加が容易になる。  
また、最終ノードのnextとしてガードノードを設定しておくことで、ノード検索時にリストを1周したか判定しやすくなる。

連結リストはリストの先頭、終端へのデータ追加が$O(1)$になるので高速に処理できる。  
しかしデータの検索は1つずつ要素を辿る必要があるので$O(N)$の計算量が必要になる。

```cpp
#include <bits/stdc++.h>
using namespace std;

class linked_list {
private:
    struct node {
        int key;
        node *p_next;
        node *p_prev;
    };
    int count;
    node *nil;      // ガードノード

public:
    // コンストラクタ
    linked_list() : count(0) {
        nil = new node();       // コンストラクタでガードノード作成
        nil->p_next = nil;
        nil->p_prev = nil;
    }
    // デストラクタ
    ~linked_list() {
        node *cur = nil->p_next;
        while (cur != nil) {
            node *next = cur->p_next;
            free(cur);
            count--;
            cur = next;
        }
    }
    // 先頭へのデータ追加
    int insert(int x) {
        node *new_node = new node();
        new_node->key = x;
        new_node->p_next = nil->p_next;
        new_node->p_prev = nil;
        nil->p_next->p_prev = new_node;
        nil->p_next = new_node;
        return ++count;
    }
    // キーによる検索
    node *list_search(int x) {
        node *cur = nil->p_next;
        // 1要素ずつ辿って検索する必要があるので計算量が
        // O(N)になる。
        while (cur != nil) {
            node *next = cur->p_next;
            if (cur->key == x) return cur;
            cur = next;
        }
        return nullptr;
    }
    // ノード削除
    int delete_node(node *del_node) {
        if (del_node == nullptr) return count;
        del_node->p_prev->p_next = del_node->p_next;
        del_node->p_next->p_prev = del_node->p_prev;
        free(del_node);
        return --count;
    }
    // 先頭ノード削除
    int delete_first() {
        // 必ずガードノードの次が先頭ノード
        return delete_node(nil->p_next);
    }
    // 終端ノード削除
    int delete_last() {
        // 必ずガードノードの前が終端ノード
        return delete_node(nil->p_prev);
    }
    // 特定キーのノード削除
    int delete_key(int x) {
        return delete_node(list_search(x));
    }
    void print_list() {
        node *cur = nil->p_next;
        bool first = true;
        while (cur != nil) {
            if (!first) cout << " ";
            first = false;
            cout << cur->key;
            cur = cur->p_next;
        }
        cout << endl;
    }
};

int main() {
    int n;
    cin >> n;

    char command[16];
    int  key;
    linked_list my_list = linked_list();
    for (int i = 0; i < n; i++) {
        scanf("%s%d", command, &key);
        if (strcmp(command, "insert") == 0) {
            my_list.insert(key);
        } else if (strcmp(command, "deleteFirst") == 0) {
            my_list.delete_first();
        } else if (strcmp(command, "deleteLast") == 0) {
            my_list.delete_last();
        } else {
            my_list.delete_key(key);
        }
    }
    my_list.print_list();

    return 0;
}
```

## 標準ライブラリのデータ構造

リスト、スタックやキューなどのデータ構造は多くの言語でライブラリや関数が提供されている。  
ただしその特徴や計算量を知った上で使用することが重要。  

C++ではSTL(Starndard Tmpllate Livrary)という標準ライブラリで提供される。  

### stack

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    stack<int> S;

    S.push(3);  // スタックに3を積む
    S.push(7);  // スタックに7を積む
    S.push(1);  // スタックに1を積む
    cout << S.size() << " ";    // スタックサイズを出力  3

    cout << S.top() << " ";     // スタック頂点を出力  1
    S.pop();                    // スタック頂点の要素を削除

    cout << S.top() << " ";     // 7
    S.pop();

    cout << S.top() << " ";     // 3

    S.push(5);

    cout << S.top() << " ";     // 5
    S.pop();

    cout << S.top() << endl;    // 3
    return 0;
}
```

`stack<int> S;`により、int型要素を管理するスタックが生成される。  
`<>`で指定した型のデータを管理できる。  

`stack`では以下の関数が定義されている(一部)。

|関数名|機能|計算量|
|---|---|---|
|size()|スタックの要素数を返す|$O(1)$|
|top()|スタックの頂点要素を返す|$O(1)$|
|pop()|スタックの頂点要素を削除する|$O(1)$|
|push(x)|スタックに要素`x`を追加する|$O(1)$|
|empty()|スタックが空の時`true`を返す|$O(1)$|

### queue

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    queue<string> Q;

    Q.push("red");
    Q.push("yellow");
    Q.push("yellow");
    Q.push("blue");

    cout << Q.front() << " ";   // red
    Q.pop();

    cout << Q.front() << " ";   // yellow
    Q.pop();

    cout << Q.front() << " ";   // yellow
    Q.pop();
    
    Q.push("green");

    cout << Q.front() << " ";   // blue
    Q.pop();

    cout << Q.front() << endl;   // green
    return 0;
}
```

`queue<string> Q;`により、string型要素を管理するキューが生成される。  
`queue`では以下の関数が定義されている(一部)。

|関数名|機能|計算量|
|---|---|---|
|size()|キューの要素数を返す|$O(1)$|
|front()|キューの先頭要素を返す|$O(1)$|
|pop()|キューの先頭要素を削除する|$O(1)$|
|push(x)|キューに要素`x`を追加する|$O(1)$|
|empty()|キューが空の時`true`を返す|$O(1)$|

### vector

`vector`は要素追加によってサイズが拡張される配列で、動的配列、可変長配列と呼ばれる。  
通常の配列はサイズ拡張不可で静的配列、固定長配列と呼ばれる。

```cpp
#include <bits/stdc++.h>
using namespace std;

void print(vector<double>& V) {
    for(int i = 0; i < V.size(); i++) {
        cout << V[i] << " ";
    }
    cout << endl;
}

int main()
{
    vector<double> V;

    V.push_back(0.1);
    V.push_back(0.2);
    V.push_back(0.3);
    V[2] = 0.4;
    print(V);   // 0.1 0.2 0.4

    V.insert(V.begin() + 2, 0.8);
    print(V);  // 0.1 0.2 0.8 0.4

    V.erase(V.begin() + 1);
    print(V);  // 0.1 0.8 0.4

    V.push_back(0.9);
    print(V);  // 0.1 0.8 0.4 0.9
    return 0;
}
```

`vector<double> V;`により、double型要素を管理するベクタが生成される。  
`vector`では以下の関数が定義されている(一部)。

|関数名|機能|計算量|
|---|---|---|
|size() |ベクタの要素数を返す|$O(1)$|
|push_back(x)|ベクタの最後に要素`x`を追加する|$O(1)$|
|pop_back()  |ベクタの最終要素を削除する|$O(1)$|
|begin()|ベクタの先頭要素を指すイテレータを返す|$O(1)$|
|end()|ベクタの末尾(最終要素の次)を指すイテレータを返す|$O(1)$|
|insert(p, x)|ベクタの`p`の位置に`x`を挿入する|$O(n)$|
|erase(p)|ベクタの`p`の位置の要素を削除する|$O(n)$|
|clear()|ベクタの全要素を削除する|$O(n)$|

イテレータとはポインタのような物。  
要素数がnのvectorに対する特定位置へのデータ挿入や削除は$O(n)$の計算量が必要になるので注意が必要。

### list

`list`は双方向連結リストを提供するSTL。  

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    list<char> L;

    L.push_front('b');      // b
    L.push_back('c');       // bc
    L.push_front('a');      // abc

    cout << L.front();      // a
    cout << L.back();       // c

    L.pop_front();          // bc
    L.push_back('d');       // bcd

    cout << L.front();      // b
    cout << L.back() << endl;   // d
    return 0;
}
```

`list<char> L;`により、char型要素を管理する双方向連結リストが生成される。  
`list`では以下の関数が定義されている(一部)。

|関数名|機能|計算量|
|---|---|---|
|size()      |リストの要素数を返す|$O(1)$|
|begin()     |リストの先頭要素を指すイテレータを返す|$O(1)$|
|end()       |リストの末尾(最終要素の次)を指すイテレータを返す|$O(1)$|
|push_front(x)|リストの先頭に要素`x`を追加する|$O(1)$|
|push_back(x)|リストの最後に要素`x`を追加する|$O(1)$|
|pop_front()  |リストの先頭要素を削除する|$O(1)$|
|pop_back()  |リストの最終要素を削除する|$O(1)$|
|insert(p, x)|リストの`p`の位置に`x`を挿入する|$O(1)$|
|erase(p)    |リストの`p`の位置の要素を削除する|$O(1)$|
|clear()     |リストの全要素を削除する|$O(n)$|

`list`は`vector`と異なり、`[]`でインデックスを指定して要素にアクセスすることはできない。  
イテレータを用いて順番にアクセスしていく必要がある。  
`vector`とは異なり、要素の挿入は$O(1)$で行うことができる。  
(双方向連結リストなので)

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_D&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_3_D&lang=ja)

### 解答

STLを使って解いた解答が以下。

```cpp
#include <bits/stdc++.h>
using namespace std;
using P = pair<int, int>;  // 2つの値を組にして管理する構造

int main() {
    int total = 0; // 水溜り総面積
    stack<int> S1; // 水溜り面積計算用スタック
    stack<P> S2;   // 各水溜り面積保持スタック
                   //   first: 水溜り開始位置
                   //   sedcond: 水溜り面積
    char ch;
    for (int idx = 0; cin >> ch; idx++) {
        if (ch == '\\') {
            S1.push(idx); // 水溜りの開始位置をpush
        }
        else if ((ch == '/') && (S1.size() > 0)) {
            int start = S1.top();
            S1.pop();              // 水溜りの開始位置を取得
            int ans = idx - start; //水溜り終了位置 - 開始位置 = 面積
            total += ans;          // 水溜り総面積に加算
            while ((S2.size() > 0) && (S2.top().first > start)) {
                // 水溜り面積保持スタックに要素がある、かつ
                //  S1とS2の水溜り開始位置が一致するまで
                // 水溜り面積を加算する
                ans += S2.top().second;
                S2.pop();
            }
            // 水溜り面積保持スタックにpush
            S2.push(make_pair(start, ans));
        }
    }
    cout << total << endl; // 水溜り総面積出力
    cout << S2.size();     // 水溜り数出力
    // listを使って順番に並べる
    list<int> result;
    while (S2.size() > 0) {
        result.push_front(S2.top().second); S2.pop();
    }
    // イテレータで水溜り面積を順次出力
    for (auto itr = result.begin(); itr != result.end(); itr++) {
        cout << " " << *itr;
    }
    cout << endl;
    return 0;
}
```
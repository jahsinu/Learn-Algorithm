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

### 解答

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

## ハッシュ法

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_C&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_C&lang=ja)

普通、ハッシュテーブル(辞書)はkeyとvalueを組みにして保存するが、この問題の場合は1つの文字列がkeyであり、value。  

### 解答

理解できていないことがいくつかあるが、重要なのは以下。

- keyがなるべくユニークになること
- ハッシュ関数の結果がコンフリクトした場合の処理  
  (コンフリクト: 異なるkeyで同じidxが算出されること)

コンフリクトの対処としては以下の2つの方法がある。

- チェーン法  
  ハッシュテーブル内の要素を連結リスト要素とし、コンフリクト発生時にリストを追加していく方法。  
  探索時、該当idxのリスト先頭が期待する値と異なれば、リストを線形探索していく。  
  コンフリクトが多くなると線形探索により計算量が増えていく。
  リストに要素追加していくので格納可能な最大数に制限がない。
- オープンアドレス法  
  コンフリクト発生時、idxに二次ハッシュを加算する方法。  
  格納可能な最大要素数が把握できないと使えない。  
  格納するデータの総数は、ハッシュテーブルサイズの80～90%程度に抑えるとよいらしい。

以下の実装はオープンアドレス法を使用している。

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

// ハッシュテーブルのサイズは素数である必要がある
// ハッシュ関数で生成できないインデックスが出ないようにするため
// nの最大数である1000000以上の素数とする
constexpr int HASH_TABLE_LEN = 1046520;
constexpr int STR_LEN = 16; // 文字列長
constexpr int COM_LEN = 8;  // コマンド文字列長
// グローバル変数は0で初期化されるので初期化処理不要
char hash_tbl[HASH_TABLE_LEN][STR_LEN];

int getChr(char ch) {
    // 文字を数値に変換する
    switch (ch) {
    case 'A': return 1; break;
    case 'C': return 2; break;
    case 'G': return 3; break;
    case 'T': return 4; break;
    default: break;
    }
    return 0;
}

ll getKey(const char str[]) {
    ll p = 1, key = 0;
    for (int i = 0; i < strlen(str); i++) {
        key += p * getChr(str[i]);
        p *= 5; // なんで5を掛ける？ 理解できていない。
    }
    return key;
}

// ハッシュ関数
int h1(ll key) { return key % HASH_TABLE_LEN; }
int h2(ll key) { return 1 + (key % (HASH_TABLE_LEN - 1)); }
// 最後に1を足すのは、インデックス算出結果が0になる可能性があるから。
// (keyがHASH_TABLE_LENの倍数になると0になる)
// 0になってしまうと関数呼び出し部のループが無限ループになってしまうので、
// 最後に1を足して無限ループを回避する。
// 調べると上記のように記載されたサイトが見つかるが、それならh1()も最後に
// 1を加算した方がいいのでは？と思った。

bool find(const char str[]) {
    ll key = getKey(str); // 文字列を数値のkeyに変換
    for (int i = 0;; i++) {
        // h1()で出したidxに異なる文字列が格納されていたら
        // h2()で出した数の分idxをずらしていく
        int idx = (h1(key) + i * h2(key)) % HASH_TABLE_LEN;
        // keyから導いたidxの値がstrと一致すればtrue
        if (strcmp(hash_tbl[idx], str) == 0) return true;
        // keyから導いたidxの値が空文字であればテーブルに存在しないのでfalse
        else if (strlen(hash_tbl[idx]) == 0) return false;
        else continue;
    }
    return false;
}

bool insert(const char str[]) {
    ll key = getKey(str); // 文字列を数値のkeyに変換
    for (int i = 0;; i++) {
        // h1()で出したidxが使用済み、かつ異なる文字列が格納されていたら
        // h2()で出した数の分idxをずらしていく
        int idx = (h1(key) + i * h2(key)) % HASH_TABLE_LEN;
        // keyから導いたidxの値がstrと一致すれば既に格納済み
        if (strcmp(hash_tbl[idx], str) == 0) return true;
        // keyから導いたidxの値が空文字であれば文字列をコピー
        else if (strlen(hash_tbl[idx]) == 0) {
            strncpy(hash_tbl[idx], str, STR_LEN - 1);
            return true;
        } else continue;
    }
    return false;
}

int main() {
    int n;
    cin >> n;
    char command[COM_LEN], str[STR_LEN];
    for (int i = 0; i < n; i++) {
        scanf("%s %s", command, str); // 入力数が多いので高速なscanf()を使用
        // コマンドはinsertかfindしかないので1文字目で判断
        if (command[0] == 'i') (void)insert(str);
        else {
            bool res = find(str);
            if (res) cout << "yes" << endl;
            else cout << "no" << endl;
        }
    }
    return 0;
}
```

## 標準ライブラリによる探索

### イテレータ

イテレータはSTLのコンテナ要素に対して反復処理を行うためのオブジェクト。  
コンテナ内の特定の位置を示すもの。  

イテレータはどの種類のコンテナでも同じ方法で要素に順次アクセスできるのが特徴。  

#### イテレータへのアクセス

|関数名|機能|
|--|--|
|begin()|コンテナの先頭イテレータを返す|
|end()|コンテナ末尾のイテレータ(最終要素の次の位置)を返す|

以下の演算子でイテレータを操作できる。

|演算子|機能|
|--|--|
|++|イテレータを次の要素に進める|
|==, !=|2つのイテレータの位置を比較する|
|=|イテレータの代入|
|*|イテレータが示す位置の値を返す|

上記の通り、イテレータの操作はほぼポインタと同じ感覚。  

```cpp
#include <bits/stdc++.h>
using namespace std;

void print(vector<int> v) {
    for (auto it = v.begin(); it != v.end(); it++) {
        cout << *it;
    }
    // イテレータの型を明示するなら以下
    // vector<int>::iterator it;
    // for (it = v.begin(); it != v.end(); it++) {
    
    cout << endl;
    return;
}

int main()
{
    int N = 4;
    vector<int> v;

    for (int i = 0; i < N; i++) {
        int x;
        cin >> x;
        v.push_back(x);
    }
    print(v);

    auto it = v.begin();
    *it = 3;    // 先頭要素 v[0] を 3 にする
    it++;       // イテレータを進める
    (*it)++;    // v[1] の値をインクリメント
    print(v);

    return 0;
}
```

```bash
# 入力
2 0 1 4

# 出力
2014
3114
```

### binary_search, lower_bound, upper_bound

STLではbinary_search, lower_boud, upper_boundという二分探索に関するライブラリが用意されている。  

### binary_search

名前の通り二分探索を行う。  
keyとして渡した値が存在するか否かを返す。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<int> A = {1, 1, 2, 2, 2, 4, 5, 5, 6, 8, 8, 8, 10, 15};
    int key1 = 5;
    int key2 = 11;

    auto itr_begin = A.begin();
    auto itr_end = A.end();
    cout << "key1: " << binary_search(itr_begin, itr_end, key1) << endl;
    cout << "key2: " << binary_search(itr_begin, itr_end, key2) << endl;
}
```

```bash
# 出力
key1: 1
key2: 0
```

### lower_bound, upper_bound

この2つはとても似ていて分かりづらい。  

- lower_bound  
  配列に二分探索を行い、指定したkey**以上**の要素のうち一番左側の要素のイテレータを返す。  
- upper_bound  
  配列に二分探索を行い、指定したkey**を超える**の要素のうち一番左側の要素のイテレータを返す。  

二分探索を行う関数なので、検索対象の配列はソートされていることが前提であることに注意。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<int> A = {1, 1, 2, 2, 2, 4, 5, 5, 6, 8, 8, 8, 10, 15};
    int key = 2;

    auto itr_begin = A.begin();
    auto itr_end = A.end();
    cout << "lower_bound: " << distance(itr_begin, lower_bound(itr_begin, itr_end, key)) << endl;
    cout << "upper_bound: " << distance(itr_begin, upper_bound(itr_begin, itr_end, key)) << endl;
}
```

```bash
# 出力
lower_bound: 2
upper_bound: 5
```

lower_boundでは**2を含めた**要素の中の、一番左の位置`A[2]`の位置が返っている。  
upper_boundでは**2を超えた**要素の中の、一番左の位置`A[5]`の位置が返っている。  
(distanceは2つのポインタの距離を返す関数)

## 応用問題

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_D&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_4_D&lang=ja)

### 解答

トラックの最大積載量を増やすとトラックに詰める荷物が増えることから、二分探索を使用して答えを出すことができる。  

まず、指定されたトラック台数と最大積載量で積める荷物の数を返す関数を用意する。  
そして、その関数に二分探索で候補とした最大積載量の値を渡して、最小の最大積載量を探す。

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

constexpr int NUM_PACKETS_MAX = 100000;     // 最大荷物数
constexpr int PACKET_WEIGHT_MAX = 10000;    // 荷物の最大重量

// 指定されたトラック台数と最大積載量で積める荷物の数を返す
int check(vector<ll>& packets, int nb_tracks, ll max_capa) {
  // 荷物のindex。
  // トラック毎に初期化しないのでこの位置で初期化。
  int idx = 0;
  // 各トラックに荷物を積む
  for (int track = 0; track < nb_tracks; track++) {
    ll total = 0;
    while (total + packets[idx] <= max_capa) {
      total += packets[idx];
      idx++;
      // 積んだ荷物の数を返す 荷物を全部積めた場合
      if (idx == packets.size()) return idx;
    }
  }
  // 積んだ荷物の数を返す 荷物を全部積む前に最大積載量に達した場合
  return idx;
}

// 荷物を積みきれる最大積載量を二分探索する
int solve(vector<ll>& packets, int nb_tracks) {
  ll left = 0;  // 最大積載量の最小値
  ll right = NUM_PACKETS_MAX * PACKET_WEIGHT_MAX;  // 最大積載量の最大値
  ll mid;
  while (right - left > 1) {
    mid = (left + right) / 2;
    // 中間の最大積載量で荷物を積みきれるかチェック
    int res = check(packets, nb_tracks, mid);
    if (res >= packets.size()) {
      // 積みきれる場合、最大積載量を減らしていく
      right = mid;
    } else {
      // 積みきれなければ、最大積載量を増やしていく
      left = mid;
    }
  }
  return right;
}

int main() {
  int nb_packets, nb_tracks;
  cin >> nb_packets >> nb_tracks;
  vector<ll> packets = vector<ll>(nb_packets);
  for (int i = 0; i < nb_packets; i++) {
    cin >> packets[i];
  }
  int res = solve(packets, nb_tracks);
  cout << res << endl;
  return 0;
}
```
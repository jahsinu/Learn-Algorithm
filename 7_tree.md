# 木

## 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_A&lang=ja)

## 解答

この問題のような、入力後に節点の数が変化しない根付き木の場合、各節点が以下の情報を持つ「左子右兄弟表現」で木を表すことができる。  

**左子右兄弟表現**
- 節点uの親
- 節点uの最も左の子
- 節点uの右の兄弟

以下、Go言語での実装例。  
左子右兄弟表現の構造体 `Node` には深さを表す `depth` 情報を追加している。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

const (
	MAX = 100005
	NIL = -1
)

type Node struct {
	parent int // 親
	left   int // 一番左の子
	right  int // 右の兄弟
	depth  int // 深さ
}

var T [MAX]Node

// 深さの情報を設定する
func setDepth(i int, d int) {
	T[i].depth = d
	if T[i].right != NIL {
		// 右の兄弟に深さを設定する
		setDepth(T[i].right, d)
	}
	if T[i].left != NIL {
		// 一番左の子に深さを設定する
		// 子なので1段深くなるため、dを加算する
		setDepth(T[i].left, d+1)
	}
	return
}

// 子供リストの文字列を取得する
func getChildrenStr(i int) string {
	c := T[i].left

	// +演算子の文字列連結は遅いので、[]byteを使用する
	// capacityを指定してappendでアロケーションが発生しないようにする
	buf := make([]byte, 0, 128)
	buf = append(buf, '[')
	for cnt := 0; c != NIL; cnt++ {
		if cnt != 0 {
			buf = append(buf, ", "...)
		}
		buf = append(buf, strconv.Itoa(c)...)
		c = T[c].right
	}
	buf = append(buf, ']')
	return string(buf)
}

// ノード種別の文字列を取得する
func getKindStr(i int) string {
	if T[i].parent == NIL {
		return "root"
	} else if T[i].left == NIL {
		return "leaf"
	}
	return "internal node"
}

func main() {
	sc := bufio.NewScanner(os.Stdin)
	sc.Split(bufio.ScanWords)
	sc.Scan()
	n, _ := strconv.Atoi(sc.Text())
	for i := 0; i < n; i++ {
		// NIL(-1)で初期化
		T[i].parent, T[i].left, T[i].right, T[i].depth = NIL, NIL, NIL, NIL
	}

	var id, degree, left, child int
	for i := 0; i < n; i++ {
		sc.Scan()
		id, _ = strconv.Atoi(sc.Text()) // node番号取得
		sc.Scan()
		degree, _ = strconv.Atoi(sc.Text()) // 子供の数を取得
		for j := 0; j < degree; j++ {
			sc.Scan()
			child, _ = strconv.Atoi(sc.Text()) // 子供のnode番号取得
			if j == 0 {
				T[id].left = child // 一番左の子を設定
			} else {
				T[left].right = child // 子の右の兄弟を設定
			}
			left = child
			T[child].parent = id // 子の親を設定
		}
	}

	// 根のnode番号を取得
	var root int
	for i := 0; i < n; i++ {
		if T[i].parent == NIL {
			root = i
			break
		}
	}

	// 各nodeの深さ情報を設定
	setDepth(root, 0)

	// 多量のデータを書き出すのでWriter使用
	wr := bufio.NewWriter(os.Stdout)
	for i := 0; i < n; i++ {
		fmt.Fprintf(
            wr,
			"node %d: parent = %d, depth = %d, %s, %s\n",
			i,
			T[i].parent,
			T[i].depth,
			getKindStr(i),
			getChildrenStr(i))
	}
	wr.Flush()

	return
}
```
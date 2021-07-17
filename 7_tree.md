# 木

## 根付き木の表現

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_A&lang=ja)

### 解答

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
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	for i := 0; i < n; i++ {
		// NIL(-1)で初期化
		T[i].parent, T[i].left, T[i].right, T[i].depth = NIL, NIL, NIL, NIL
	}

	for i := 0; i < n; i++ {
		s.Scan()
		id, _ := strconv.Atoi(s.Text()) // node番号取得
		s.Scan()
		d, _ := strconv.Atoi(s.Text()) // 子供の数を取得
		var l int
		for j := 0; j < d; j++ {
			s.Scan()
			c, _ := strconv.Atoi(s.Text()) // 子供のnode番号取得
			if j == 0 {
				T[id].left = c // 一番左の子を設定
			} else {
				T[l].right = c // 子の右の兄弟を設定
			}
			l = c
			T[c].parent = id // 子の親を設定
		}
	}

	// 根のnode番号を取得
	var r int
	for i := 0; i < n; i++ {
		if T[i].parent == NIL {
			r = i
			break
		}
	}

	// 各nodeの深さ情報を設定
	setDepth(r, 0)

	// 多量のデータを書き出すのでWriter使用
	w := bufio.NewWriter(os.Stdout)
	for i := 0; i < n; i++ {
		fmt.Fprintf(
			w,
			"node %d: parent = %d, depth = %d, %s, %s\n",
			i,
			T[i].parent,
			T[i].depth,
			getKindStr(i),
			getChildrenStr(i))
	}
	w.Flush()
	return
}
```

## 二分木の表現

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_7_B&lang=ja)

### 解答

この問題のような、入力後に節点の数が変化しない二分木の場合、各節点について以下の情報持つことで木を表すことができる。  

**二分木の表現**
- 節点uの親
- 節点uの左の子
- 節点uの右の子

以下、Go言語での実装例。  
二分木表現の構造体 `Node` には深さを表す `depth`、高さを表す `height` の情報を追加している。

```go
package main

import (
	"bufio"
	"fmt"
	"math"
	"os"
	"strconv"
)

const (
	MAX = 32
	NIL = -1
)

type Node struct {
	parent int // 親
	left   int // 左の子
	right  int // 右の子
	depth  int // 深さ
	height int // 高さ
}

var T [MAX]Node

// 深さの情報を設定する
func setDepth(i int, d int) {
	T[i].depth = d
	if T[i].right != NIL {
		// 右の子に深さを設定する
		setDepth(T[i].right, d+1)
	}
	if T[i].left != NIL {
		// 左の子に深さを設定する
		setDepth(T[i].left, d+1)
	}
	return
}

// 深さの情報を設定する
func setHeight(i int) int {
	var lh, rh int
	if T[i].left != NIL {
		lh = setHeight(T[i].left) + 1
	}
	if T[i].right != NIL {
		rh = setHeight(T[i].right) + 1
	}
	T[i].height = int(math.Max(float64(lh), float64(rh)))
	return T[i].height
}

// 兄弟のノードIDを取得する
func getSibling(i int) int {
	if T[i].parent == NIL {
		return NIL
	}
	if p := T[i].parent; T[p].left == i {
		return T[p].right
	} else {
		return T[p].left
	}
}

// 節点の子の数を取得する
func getDegree(i int) int {
	var d int
	if T[i].left != NIL {
		d++
	}
	if T[i].right != NIL {
		d++
	}
	return d
}

// ノード種別の文字列を取得する
func getKindStr(i int) string {
	if T[i].parent == NIL {
		return "root"
	} else if T[i].left == NIL && T[i].right == NIL {
		return "leaf"
	}
	return "internal node"
}

func main() {
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	for i := 0; i < n; i++ {
		// NIL(-1)で初期化
		T[i].parent, T[i].left, T[i].right = NIL, NIL, NIL
		T[i].depth, T[i].height = NIL, NIL
	}

	for i := 0; i < n; i++ {
		s.Scan()
		id, _ := strconv.Atoi(s.Text()) // node番号取得
		s.Scan()
		T[id].left, _ = strconv.Atoi(s.Text()) // 左の子を設定
		s.Scan()
		T[id].right, _ = strconv.Atoi(s.Text()) // 右の子を設定
		if T[id].left != NIL {
			T[T[id].left].parent = id
		}
		if T[id].right != NIL {
			T[T[id].right].parent = id
		}
	}

	// 根のnode番号を取得
	var r int
	for i := 0; i < n; i++ {
		if T[i].parent == NIL {
			r = i
			break
		}
	}

	// 各nodeの深さ情報を設定
	setDepth(r, 0)
	// 各nodeの高さ情報を設定
	setHeight(r)

	// 多量のデータを書き出すのでWriter使用
	w := bufio.NewWriter(os.Stdout)
	for i := 0; i < n; i++ {
		fmt.Fprintf(
			w,
			"node %d: parent = %d, sibling = %d, degree = %d, depth = %d, height = %d, %s\n",
			i,
			T[i].parent,
			getSibling(i),
			getDegree(i),
			T[i].depth,
			T[i].height,
			getKindStr(i))
	}
	w.Flush()
	return
}
```
# 二分探索木

動的な集合を扱うデータ構造として、データの追加・削除・探索を行うことができる連結リストがあるが、連結リストは探索にO(n)の計算量を必要とする。  
動的な木構造を用いれば、データの追加・削除・探索をより効率的に行うことができる。

## 二分探索木: 挿入

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_A&lang=ja)

### 解答

二分探索木のノード情報は、キー情報、親へのポインタ、左の子へのポインタ、右の子へのポインタとして実装する。  
各節点をポインタで繋げていく。

```go
type Node struct {
	key    int
	parent *Node
	left   *Node
	right  *Node
}
```

問題で例示された`insert()`を実装し、二分探索木の条件を保つようにデータを正しい位置に挿入する必要がある。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

type Node struct {
	key    int
	parent *Node
	left   *Node
	right  *Node
}

func (n *Node) preorder() {
	if n == nil {
		return
	}
	fmt.Fprintf(wr, " %d", n.key)
	n.left.preorder()
	n.right.preorder()
	return
}

func (n *Node) inorder() {
	if n == nil {
		return
	}
	n.left.inorder()
	fmt.Fprintf(wr, " %d", n.key)
	n.right.inorder()
	return
}

const MAX = 500000

var root *Node
var wr = bufio.NewWriter(os.Stdout)

func insert(k int) {
	// 追加するノードを作成
	z := new(Node)
	z.key = k
	z.left, z.right = nil, nil

	// ノード追加場所を探す
	x := root         // 探索の開始位置
	var y *Node = NIL // 探索完了位置から見た親

	for x != nil {
		y = x // 親を設定
		if z.key < x.key {
			x = x.left // 左の子へ移動
		} else {
			x = x.right // 右の子へ移動
		}
	}

	// 追加ノードの親を設定
	z.parent = y

	if y == nil { // 二分探索木が空の場合
		root = z
	} else if z.key < y.key {
		y.left = z // zをyの左の子にする
	} else {
		y.right = z // zをyの右の子にする
	}
}

func main() {
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	for i := 0; i < n; i++ {
		s.Scan()
		c := s.Text()
		if c == "insert" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			insert(k)
		} else if c == "print" {
			root.inorder()
			fmt.Fprintf(wr, "\n")
			root.preorder()
			fmt.Fprintf(wr, "\n")
			wr.Flush()
		}
	}
}
```
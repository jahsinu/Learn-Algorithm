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

func (n *Node) preorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	fmt.Fprintf(wr, " %d", n.key)
	n.left.preorder(wr)
	n.right.preorder(wr)
	return
}

func (n *Node) inorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	n.left.inorder(wr)
	fmt.Fprintf(wr, " %d", n.key)
	n.right.inorder(wr)
	return
}

func (n *Node) insert(k int) {
	if n.key == NIL {
		// 二分探索木が空の場合
		n.key = k
		n.parent, n.left, n.right = nil, nil, nil
		return
	}

	// 追加するノードを作成
	z := &Node{key: k, left: nil, right: nil}

	// ノード追加場所を探す
	x := n            // 探索の開始位置
	var y *Node = nil // 探索完了位置から見た親
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
	if z.key < y.key {
		y.left = z // zをyの左の子にする
	} else {
		y.right = z // zをyの右の子にする
	}
	return
}

func NewNode() (ret *Node) {
	ret = &Node{key: NIL, parent: nil, left: nil, right: nil}
	return
}

const MAX = 500000
const NIL = -1

func main() {
	wr := bufio.NewWriter(os.Stdout)
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	root := NewNode()
	for i := 0; i < n; i++ {
		s.Scan()
		c := s.Text()
		if c == "insert" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			root.insert(k)
		} else if c == "print" {
			root.inorder(wr)
			fmt.Fprintf(wr, "\n")
			root.preorder(wr)
			fmt.Fprintf(wr, "\n")
			wr.Flush()
		}
	}
}
```

## 二分探索木: 探索

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_B&lang=ja)

### 解答

挿入のソースに`search()`を追加し、findコマンドの入力に対応するのみ。  
入力コマンドが"find"だったら、キーを`search()`に渡す。

#### search()

指定されたキーを持つノードが見つかるまで、木を探索する。  
キーの値がノードのキーより小さいか否かで、左の子へ進むか右の子へ進むか判定する。

```go
func (n *Node) search(k int) *Node {
	for n != nil && n.key != k {
		if k < n.key {
			n = n.left // 左の子へ移動
		} else {
			n = n.right // 右の子へ移動
		}
	}
	return n
}
```

<details><summary>ソース全文</summary> <p>

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

func (n *Node) preorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	fmt.Fprintf(wr, " %d", n.key)
	n.left.preorder(wr)
	n.right.preorder(wr)
	return
}

func (n *Node) inorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	n.left.inorder(wr)
	fmt.Fprintf(wr, " %d", n.key)
	n.right.inorder(wr)
	return
}

func (n *Node) insert(k int) {
	if n.key == NIL {
		// 二分探索木が空の場合
		n.key = k
		n.parent, n.left, n.right = nil, nil, nil
		return
	}

	// 追加するノードを作成
	z := &Node{key: k, left: nil, right: nil}

	// ノード追加場所を探す
	x := n            // 探索の開始位置
	var y *Node = nil // 探索完了位置から見た親
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
	if z.key < y.key {
		y.left = z // zをyの左の子にする
	} else {
		y.right = z // zをyの右の子にする
	}
	return
}

func (n *Node) search(k int) *Node {
	for n != nil && n.key != k {
		if k < n.key {
			n = n.left // 左の子へ移動
		} else {
			n = n.right // 右の子へ移動
		}
	}
	return n
}

func NewNode() (ret *Node) {
	ret = &Node{key: NIL, parent: nil, left: nil, right: nil}
	return
}

const MAX = 500000
const NIL = -1

func main() {
	wr := bufio.NewWriter(os.Stdout)
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	root := NewNode()
	for i := 0; i < n; i++ {
		s.Scan()
		c := s.Text()
		if c == "insert" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			root.insert(k)
		} else if c == "print" {
			root.inorder(wr)
			fmt.Fprintf(wr, "\n")
			root.preorder(wr)
			fmt.Fprintf(wr, "\n")
			wr.Flush()
		} else if c == "find" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			if root.search(k) != nil {
				fmt.Fprintf(wr, "yes\n")
			} else {
				fmt.Fprintf(wr, "no\n")
			}
			wr.Flush()
		}
	}
}
```

</p> </details>
　  
　  

## 二分探索木: 削除

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_C&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_8_C&lang=ja)

### 解答

探索のソースに`deleteNode()`を追加し、deleteコマンドの入力に対応する。
入力コマンドが"delete"だったら、キーをsearch()に渡し、返却されたノードを`deleteNode()`に渡す。

ノード削除のアルゴリズムは問題で示された内容で実装する。

#### deleteNode()

1. 削除する対象が指定されたノード `z` ではないケースがあるので、まずは削除対象 `y` を決める。  
1. `y` の親と `y` の子のポインタをつなぎ替えるため、対象となる子 `x` を決める。  
1. ポインタの繋ぎ変えは、まず `x` の親が `y` の親となるようにする。  
1. 次に `y` の親の子が `x` になるよう、`y` が根なのか、左の子なのか右の子なのか調べてポインタをつなぎ替える。  

```go
func (n *Node) deleteNode(z *Node) {
	var x, y *Node
	// 削除対象を節点yとする
	if z.left == nil || z.right == nil {
		y = z // zが子を持たない、または子が1つの場合
	} else {
		y = z.getSuccessor() // zが子を2つ持つ場合、次節点を探す
	}

	// yの子x(yと入れ替える対象)を決める
	if y.left != nil {
		x = y.left
	} else {
		x = y.right
	}

    // 子から親のポインタ繋ぎ変え
	if x != nil {
		x.parent = y.parent // xの親として、yの親を設定
	}

    // 親から子のポインタ繋ぎ変え
	if y.parent == nil {
		*n = *x // yが根の場合、xを木の根とする
	} else if y == y.parent.left {
		y.parent.left = x
	} else {
		y.parent.right = x
	}

	if y != z { // zの次節点をyとした場合
		z.key = y.key
	}
}
```

#### getSuccessor()

次節点とは対象ノードの次に大きいキーを持ったノードのこと。  
二分探索木の構造上、対象ノードの右部分木の左端(最小)のノードとなる。  
対象ノードが右の子を持たない場合は、親をたどり、最初に左の子になっているノードの親が次節点となる。  
ただし、この問題では削除対象に2つの子がある場合にこの関数が呼び出されるので、親をたどるケースはない。

```go
func (n *Node) getSuccessor() *Node {
	// 右の子がいれば、右部分木から最小キーのノードを返す
	if n.right != nil {
		return n.right.getMinimum()
	}

	// 親をたどり、最初に「左の子になっている節点の親」を返す
	y := n.parent
	for y != nil && n == y.right { // 自分(n)が親(y)の右の子である間ループ
		n = y
		y = y.parent
	}
	return y
}
```

#### getMinimum()

左の子がなくなるまで木を探索する。

```go
func (n *Node) getMinimum() *Node {
	for n.left != nil {
		n = n.left
	}
	return n
}
```

<details><summary>ソース全文</summary> <p>

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

func (n *Node) preorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	fmt.Fprintf(wr, " %d", n.key)
	n.left.preorder(wr)
	n.right.preorder(wr)
	return
}

func (n *Node) inorder(wr *bufio.Writer) {
	if n == nil {
		return
	}
	n.left.inorder(wr)
	fmt.Fprintf(wr, " %d", n.key)
	n.right.inorder(wr)
	return
}

func (n *Node) insert(k int) {
	if n.key == NIL {
		// 二分探索木が空の場合
		n.key = k
		n.parent, n.left, n.right = nil, nil, nil
		return
	}

	// 追加するノードを作成
	z := &Node{key: k, left: nil, right: nil}

	// ノード追加場所を探す
	x := n            // 探索の開始位置
	var y *Node = nil // 探索完了位置から見た親
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
	if z.key < y.key {
		y.left = z // zをyの左の子にする
	} else {
		y.right = z // zをyの右の子にする
	}
	return
}

func (n *Node) search(k int) *Node {
	for n != nil && n.key != k {
		if k < n.key {
			n = n.left // 左の子へ移動
		} else {
			n = n.right // 右の子へ移動
		}
	}
	return n
}

func (n *Node) getMinimum() *Node {
	for n.left != nil {
		n = n.left
	}
	return n
}

func (n *Node) getSuccessor() *Node {
	// 右の子がいれば、右部分木から最小キーのノードを返す
	if n.right != nil {
		return n.right.getMinimum()
	}

	// 親をたどり、最初に「左の子になっている節点の親」を返す
	y := n.parent
	for y != nil && n == y.right { // 自分(n)が親(y)の右の子である間ループ
		n = y
		y = y.parent
	}
	return y
}

func (n *Node) deleteNode(z *Node) {
	var x, y *Node
	// 削除対象を節点yとする
	if z.left == nil || z.right == nil {
		y = z
	} else {
		y = z.getSuccessor()
	}

	// yの子xを決める
	if y.left != nil {
		x = y.left
	} else {
		x = y.right
	}

	if x != nil {
		x.parent = y.parent // xの親を設定
	}

	if y.parent == nil {
		*n = *x // yが根の場合、xを木の根とする
	} else if y == y.parent.left {
		y.parent.left = x
	} else {
		y.parent.right = x
	}

	if y != z {
		z.key = y.key
	}
}

func NewNode() (ret *Node) {
	ret = &Node{key: NIL, parent: nil, left: nil, right: nil}
	return
}

const MAX = 500000
const NIL = -1

func main() {
	wr := bufio.NewWriter(os.Stdout)
	s := bufio.NewScanner(os.Stdin)
	s.Split(bufio.ScanWords)
	s.Scan()
	n, _ := strconv.Atoi(s.Text())
	root := NewNode()
	for i := 0; i < n; i++ {
		s.Scan()
		c := s.Text()
		if c == "insert" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			root.insert(k)
		} else if c == "print" {
			root.inorder(wr)
			fmt.Fprintf(wr, "\n")
			root.preorder(wr)
			fmt.Fprintf(wr, "\n")
			wr.Flush()
		} else if c == "find" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			if root.search(k) != nil {
				fmt.Fprintf(wr, "yes\n")
			} else {
				fmt.Fprintf(wr, "no\n")
			}
			wr.Flush()
		} else if c == "delete" {
			s.Scan()
			k, _ := strconv.Atoi(s.Text())
			root.deleteNode(root.search(k))
		}
	}
}
```
</details> </p>
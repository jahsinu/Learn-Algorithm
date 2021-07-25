# ヒープ

キューやスタックと異なり、データの到着順ではなく、データが持つあるキーを基準に優先度が高い物から取り出す優先度付きキューは高等的アルゴリズムで重要な役割を果たす。  
優先度付きキューは二分探索木を応用して実現できるが、バランスの良い木を保ちながら効率的な操作を行うには工夫が必要で、実装は簡単ではない。  
二分ヒープ呼ばれるデータ構造を用いると比較的容易に優先度付きキューを実装できる。

## 完全二分木

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_9_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_9_A&lang=ja)

### 解答

問題に記載された親、左の子、右の子の添字の算出方法を実装し、ヒープの範囲内に収まっていれば出力を行う。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

const MAX = 256

var sc = bufio.NewScanner(os.Stdin)
var wr = bufio.NewWriter(os.Stdout)

func isInRange(t []int, v int) bool {
	if 0 < v && v < len(t) {
		return true
	}
	return false
}

func main() {
	defer wr.Flush()
	t := make([]int, 1, 256)
	sc.Split(bufio.ScanWords)
	sc.Scan()
	h, _ := strconv.Atoi(sc.Text())
	for i := 0; i < h; i++ {
		sc.Scan()
		v, _ := strconv.Atoi(sc.Text())
		t = append(t, v)
	}
	for i := 1; i < h+1; i++ {
		// ノード番号、ノードのキー
		fmt.Fprintf(wr, "node %d: key = %d, ", i, t[i])
		// 親
		if p := i / 2; isInRange(t, p) {
			fmt.Fprintf(wr, "parent key = %d, ", t[p])
		}
		// 左の子
		if l := 2 * i; isInRange(t, l) {
			fmt.Fprintf(wr, "left key = %d, ", t[l])
		}
		// 右の子
		if r := 2*i + 1; isInRange(t, r) {
			fmt.Fprintf(wr, "right key = %d, ", t[r])
		}
		fmt.Fprintf(wr, "\n")
	}
}
```

## 最大ヒープ

## 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_9_B&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_9_B&lang=ja)

## 解答

問題に例示された関数を実装することで解ける。  
`buildMaxHeap()`では、子を持つ節点の中で添え字が最大の節点 `s` から逆順に`maxHeapify()`を行う。  
ここで `s` は `ヒープの要素数 / 2` になる。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

const MAX = 500004

var sc = bufio.NewScanner(os.Stdin)
var wr = bufio.NewWriter(os.Stdout)

func left(i int) int  { return 2 * i }
func right(i int) int { return 2*i + 1 }

func maxHeapify(A []int, i int) {
	l, r := left(i), right(i)
	// 左の子、自分、右の子で値が最大のノードを選ぶ
	var largest int
	if l < len(A) && A[l] > A[i] {
		largest = l
	} else {
		largest = i
	}
	if r < len(A) && A[r] > A[largest] {
		largest = r
	}

	if largest != i { // iの子の方が値が大きい場合
		A[i], A[largest] = A[largest], A[i] // 値を入れ替え
		maxHeapify(A, largest)              // 再帰呼び出し
	}
}

func buildMaxHeap(A []int) {
	for i := (len(A) - 1) / 2; i > 0; i-- {
		maxHeapify(A, i)
	}
}

func main() {
	defer wr.Flush()
	A := make([]int, 1, MAX)
	sc.Split(bufio.ScanWords)
	sc.Scan()
	H, _ := strconv.Atoi(sc.Text())
	for i := 0; i < H; i++ {
		sc.Scan()
		v, _ := strconv.Atoi(sc.Text())
		A = append(A, v)
	}
	buildMaxHeap(A)
	for i := 1; i < len(A); i++ {
		fmt.Fprintf(wr, " %d", A[i])
	}
	fmt.Fprintf(wr, "\n")
}
```

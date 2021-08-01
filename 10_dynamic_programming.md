# 動的計画法

動的計画法は最適な解を求めるための数学的な考え方でもあり、組み合わせ最適化問題や画像解析など、様々な応用問題を解くためのアルゴリズムで使用されている。

## 動的計画法とは

ある計算式に対して、一度計算した結果をメモリに保存しておき、同じ計算を繰り返し行う無駄を避けつつ、それらを再利用して効率化を図ることはプログラミングやアルゴリズムの設計を行う上で有効なアプローチとなる。  
その手法の一つが **動的計画法**。

再帰・分割統治法で解いた、全探索の問題(ALDS1_5_A)を動的計画法を用いて解くと以下のようになる。  
これにより、O(2^n)の全探索アルゴリズムが、O(nm)のアルゴリズムに改善される。  
(ただし、O(nm)のメモリ量が必要となる)

再帰を使った方法は、メモ化再帰とも呼ばれる。  
漸化式を立て、ループ処理で最適解を計算することもできる。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

const MAX_I = 24
const MAX_M = 2004

var sc = bufio.NewScanner(os.Stdin)
var wr = bufio.NewWriter(os.Stdout)

// 計算結果を保持しておく領域
// 未計算:0 true:1 false:-1
var dp [MAX_I][MAX_M]int

func solve(A []int, i int, mi int) bool {
	if mi == 0 {
		return true
	}
	if mi < 0 || i >= len(A) {
		return false
	}

	// 計算済みの結果があるか確認
	if dp[i][mi] != 0 {
		return dp[i][mi] == 1
	}

	// 計算結果を保持しつつ、返却
	if solve(A, i+1, mi) {
		dp[i][mi] = 1
	} else if solve(A, i+1, mi-A[i]) {
		dp[i][mi] = 1
	} else {
		dp[i][mi] = -1
	}
	return dp[i][mi] == 1
}

func main() {
	defer wr.Flush()
	sc.Split(bufio.ScanWords)

	sc.Scan()
	n, _ := strconv.Atoi(sc.Text())
	A := make([]int, n)
	for i := 0; i < n; i++ {
		sc.Scan()
		A[i], _ = strconv.Atoi(sc.Text())
	}

	sc.Scan()
	q, _ := strconv.Atoi(sc.Text())
	for i := 0; i < q; i++ {
		sc.Scan()
		mi, _ := strconv.Atoi(sc.Text())
		if solve(A, 0, mi) {
			fmt.Fprintf(wr, "yes\n")
		} else {
			fmt.Fprintf(wr, "no\n")
		}
	}
}
```

## フィボナッチ数列

### 問題

[https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_10_A&lang=ja](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=ALDS1_10_A&lang=ja)

### 解答

問題文の公式を実装すれば良い。  
ただし、この公式は同じ計算を複数回繰り返す可能性があるので、メモ化再帰を行う。  
また最大でも44項なので、漸化式によって先に全項を算出する方法も有効。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

const MAX = 44

var sc = bufio.NewScanner(os.Stdin)
var wr = bufio.NewWriter(os.Stdout)

func main() {
	defer wr.Flush()
	sc.Split(bufio.ScanWords)

	// **********************************
	// メモ化再帰
	// **********************************
	dp := make([]int, MAX+1)
	// 再帰クロージャの宣言
	var fib func(n int) int
	// 再帰クロージャの定義
	fib = func(n int) int {
		// 計算結果を保持済みなら、保持している計算結果を返却
		if dp[n] != 0 {
			return dp[n]
		}
		if n == 0 || n == 1 {
			dp[n] = 1 // 計算結果を保持
			return dp[n]
		}
		dp[n] = fib(n-2) + fib(n-1) // 計算結果を保持
		return dp[n]
	}

	// **********************************
	// 漸化式
	// **********************************
	fib2 := make([]int, MAX+1)
	fib2[0], fib2[1] = 1, 1
	for i := 2; i < len(fib2); i++ {
		fib2[i] = fib2[i-2] + fib2[i-1]
	}

	sc.Scan()
	n, _ := strconv.Atoi(sc.Text())
	fmt.Fprintf(wr, "%d\n", fib(n))
	fmt.Fprintf(wr, "%d\n", fib2[n])
}
```

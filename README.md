# glua

embed lua script language for Go
modified so can use ubuntu's lib directory directly

go luajit glue,very very fast, support lua coroutine

## Demo
easy use, like:
```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/kokizzu/go-lua"
)

func main() {

	ts := time.Now()
	res, err := glua.NewAction().WithScript(`
	function fib(n)
		if n == 0 then
			return 0
		elseif n == 1 then
			return 1
		end
		return fib(n-1) + fib(n-2)
	end
	`).WithEntrypoint("fib").AddParam(35).Execute(context.Background())

	fmt.Println("cost:", time.Now().Sub(ts))
	fmt.Println(res, err)
}

```
## Benchmark

| | fib(35) | fibt(35) |  Language (Type)  |
| :--- |    ---: |     ---: |  :---: |
| [wassmer-go](https://github.com/wasmerio/wasmer-go) | `57ms` | `` | Wasm |
| [**glua**](https://github.com/kokizzu/go-lua) | `110ms` | `1.5ms` | Luajit(VM) |
| [Tengo](https://github.com/d5/tengo) | `2,315ms` | `3ms` | Tengo (VM) |
| [go-lua](https://github.com/Shopify/go-lua) | `4,028ms` | `3ms` | Lua (VM) |
| [GopherLua](https://github.com/yuin/gopher-lua) | `4,409ms` | `3ms` | Lua (VM) |
| [goja](https://github.com/dop251/goja) | `5,194ms` | `4ms` | JavaScript (VM) |
| [starlark-go](https://github.com/google/starlark-go) | `6,954ms` | `3ms` | Starlark (Interpreter) |
| [gpython](https://github.com/go-python/gpython) | `11,324ms` | `4ms` | Python (Interpreter) |
| [Yaegi](https://github.com/containous/yaegi) | `4,715ms` | `10ms` | Yaegi (Interpreter) |
| [otto](https://github.com/robertkrimen/otto) | `48,539ms` | `6ms` | JavaScript (Interpreter) |
| [Anko](https://github.com/mattn/anko) | `52,821ms` | `6ms` | Anko (Interpreter) |
| - | - | - | - |
| Go | `47ms` | `2ms` | Go (Native) |
| Lua | `756ms` | `2ms` | Lua (Native) |
| Python | `1,907ms` | `14ms` | Python2 (Native) |

_* [fib(35)](https://github.com/d5/tengobench/blob/master/code/fib.tengo):
Fibonacci(35)_  
_* [fibt(35)](https://github.com/d5/tengobench/blob/master/code/fibtc.tengo):
[tail-call](https://en.wikipedia.org/wiki/Tail_call) version of Fibonacci(35)_  
_* **Go** does not read the source code from file, while all other cases do_  

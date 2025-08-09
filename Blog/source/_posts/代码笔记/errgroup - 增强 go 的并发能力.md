---
title: errgroup - golang sync 的扩展库
date: 2025-08-09 15:07:46
categories:
  - 代码笔记
tags:
  - go3方库
  - golang
archive: true
hide: false
---
# 介绍

## errgroup 是什么？


errgroup 是Go官方扩展库 `golang.org/x/sync` 中的并发原语，用于管理多个 goroutine 的协同执行和错误处理。它解决了标准库 `sync.WaitGroup` 无法便捷传递错误的问题，支持在并发任务中统一处理错误，并支持在首个错误发生时取消所有任务。

## 核心特性

1. **错误聚合与传播**  
    **当任意一个 goroutine 返回错误时，errgroup 会立即取消其他未完成的任务，并将错误传递给主 goroutine，避免资源浪费。**
2. **批量任务取消**  
    通过 `context.Context` 实现任务组的级联取消，一旦某个任务失败，整个任务组会被终止。
3. **超时控制**  
    支持结合 `context.WithTimeout` 设置任务组超时时间，防止长时间阻塞。

## 与标准库 `sync.WaitGroup` 的对比

相较于 `sync.WaitGroup` 仅提供等待机制，errgroup增加了以下能力：

- 错误自动收集与传播；
- 任务取消的原子性操作；
- 更简洁的并发任务管理代码结构。


# 使用案例

## 安装

```shell
go get github.com/golang/sync
```

## 使用标准库的 `sync.WaitGroup` 收集并发错误


```golang
package main

import (
  "fmt"
  "net/http"
  "sync"
)

func main() {
  var urls = []string{
    "http://www.baidu.org/",          // 错误URL
    "http://www.bilibili.com/",       // 正常URL
    "http://www.somestupidname.com/", // 正常URL
  }

  // 1. 定义错误集合（类似K8s收集所有错误）
  var errors []error
  // 2. 定义互斥锁，保证并发安全
  var mu sync.Mutex
  var wg sync.WaitGroup

  for _, url := range urls {
    wg.Add(1)
    // 3. 传入url作为参数，避免goroutine共享循环变量（关键！）
    go func(u string) {
      defer wg.Done()
      resp, err := http.Get(u)
      if err != nil {
      
        // 加锁保护错误集合写入
        mu.Lock()
        errors = append(errors, fmt.Errorf("访问 %s 失败: %v", u, err))
        mu.Unlock()
        return
      }
      defer resp.Body.Close()
      fmt.Printf("访问 %s 成功，状态码: %s\n", u, resp.Status)
    }(url) // 传入当前迭代的url
  }

  wg.Wait() // 等待所有任务完成

  // 4. 打印所有收集到的错误（类似K8s汇总错误信息）
  if len(errors) > 0 {
    fmt.Println("\n收集到以下错误：")
    for i, err := range errors {
      fmt.Printf("错误 %d: %v\n", i+1, err)
    }
  } else {
    fmt.Println("\n所有任务执行成功，无错误")
  }
}
```

执行结果：
```shell
访问 http://www.bilibili.com/ 成功，状态码: 200 OK
访问 http://www.somestupidname.com/ 成功，状态码: 200 OK

收集到以下错误：
错误 1: 访问 http://www.baidu.org/ 失败: Get "http://www.baidu.org/": EOF
```

这里必须要把所有的并发任务执行完成后，才可以返回所有的 goroutinue，而且还需要声明两个变量 errors 和 mutex 来确保并发安全。

## 使用 `errgroup` 来收集并发错误

安装 `go get golang.org/x/sync`

下面我们看看如果使用了 errgroup 可以怎么做：

### 1.  常规用法  
使用 `errgroup` 来收集并发错误：

```golang
package main

import (
  "fmt"
  "net/http"
  "sync"

  "golang.org/x/sync/errgroup"
)

  

func main() {
  var urls = []string{
    "http://www.baidu.org/",           // 错误URL
    "http://www.bilibili.com/",        // 正常URL
    "http://www.somestupidname.xxyy/", // 错误URL
  }
  
  // 1. 定义错误集合，用于存储所有错误
  var allErrors []error
  // 2. 互斥锁，保证多个goroutine并发写入错误时的安全
  var mu sync.Mutex
  // 创建errgroup
  var g errgroup.Group

  for _, url := range urls {
    // 3. 传入当前url作为参数，避免goroutine共享循环变量
    u := url
    g.Go(func() error {
      resp, err := http.Get(u)
      if err != nil {
      
        // 加锁保护错误集合的写入
        mu.Lock()
        // 存储详细错误信息（包含URL）
        allErrors = append(allErrors, fmt.Errorf("访问 %s 失败: %w", u, err))
        mu.Unlock()
        
        // 返回错误（errgroup会记录第一个错误，但我们主要靠allErrors收集所有）
        return err
      }
      defer resp.Body.Close()
      fmt.Printf("访问 %s 成功，状态码: %s\n", u, resp.Status)
      return nil
    })
  }

  // 等待所有goroutine完成（无论是否有错误）
  _ = g.Wait() // 忽略errgroup返回的第一个错误，我们关注allErrors

  // 4. 打印所有收集到的错误
  if len(allErrors) > 0 {
    fmt.Println("\n收集到所有错误：")
    for i, err := range allErrors {
      fmt.Printf("错误 %d: %v\n", i+1, err)
    }
  } else {
    fmt.Println("\n所有请求均成功，无错误")
  }
}
```

执行结果：
```shell
访问 http://www.bilibili.com/ 成功，状态码: 200 OK

收集到所有错误：
错误 1: 访问 http://www.baidu.org/ 失败: Get "http://www.baidu.org/": EOF
错误 2: 访问 http://www.somestupidname.xxyy/ 失败: Get "http://www.somestupidname.xxyy/": EOF
```
### 2. 取消上下文
如果收集到一个错误后立刻取消其他 goroutinue，避免资源浪费，并在 `Wait` 方法中返回第一个非 `nil` 的错误：

```golang
package main

import (
    "context"
    "fmt"
    "net/http"
    "sync"

    "golang.org/x/sync/errgroup"
)

func main() {
    var urls = []string{
        "https://www.baidu.org/",          // 错误URL
        "https://www.bilibili.com/",       // 正常URL
        "https://www.somestupidname.xxyy/", // 错误URL
    }

    // 1. 创建可取消的上下文
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    // 2. 定义错误集合和互斥锁
    var allErrors []error
    var mu sync.Mutex

    // 3. 创建带上下文的errgroup
    g, ctx := errgroup.WithContext(ctx)

    for _, url := range urls {
        u := url // 避免goroutine共享循环变量
        g.Go(func() error {
            // 4. 创建带上下文的HTTP请求
            req, err := http.NewRequestWithContext(ctx, "GET", u, nil)
            if err != nil {
                mu.Lock()
                allErrors = append(allErrors, fmt.Errorf("创建请求 %s 失败: %w", u, err))
                mu.Unlock()
                return err
            }

            // 发送请求
            resp, err := http.DefaultClient.Do(req)
            if err != nil {
                mu.Lock()
                allErrors = append(allErrors, fmt.Errorf("访问 %s 失败: %w", u, err))
                mu.Unlock()
                return err // 触发上下文取消
            }
            defer resp.Body.Close()

            // 检查上下文是否已取消
            select {
            case <-ctx.Done():
                mu.Lock()
                allErrors = append(allErrors, fmt.Errorf("请求 %s 被取消: %w", u, ctx.Err()))
                mu.Unlock()
                return ctx.Err()
            default:
                fmt.Printf("访问 %s 成功，状态码: %s\n", u, resp.Status)
                return nil
            }
        })
    }

    // 5. 等待所有goroutine完成
    if err := g.Wait(); err != nil {
        fmt.Println("Error: ", err)
    }

    // 6. 打印所有错误
    if len(allErrors) > 0 {
        fmt.Println("\n收集到所有错误：")
        for i, err := range allErrors {
            fmt.Printf("错误 %d: %v\n", i+1, err)
        }
    } else {
        fmt.Println("\n所有请求均成功，无错误")
    }
}
    
```

执行结果：
```shell
Error:  Get "https://www.somestupidname.xxyy/": EOF

收集到所有错误：
错误 1: 访问 https://www.somestupidname.xxyy/ 失败: Get "https://www.somestupidname.xxyy/": EOF
错误 2: 访问 https://www.bilibili.com/ 失败: Get "https://www.bilibili.com/": context canceled
错误 3: 访问 https://www.baidu.org/ 失败: Get "https://www.baidu.org/": EOF
```

这里我们可以看到错误 2 的错误原因是上下文被取消造成的。

### 3. 限制并发数量

```golang
package main

import (
	"context"
	"fmt"
	"sync"
	"time"

	"golang.org/x/sync/errgroup"
)

func main() {
	tasks := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

	// 1. 并发计数器：记录当前正在执行的任务数
	var currentConcurrency int
	// 互斥锁：保护并发计数器的读写安全
	var mu sync.Mutex

	// 创建errgroup并限制最大并发数为2
	g, ctx := errgroup.WithContext(context.Background())
	g.SetLimit(2) // 最大并发数2

	for _, task := range tasks {
		taskID := task
		g.Go(func() error {
			// 2. 任务开始：并发数+1
			mu.Lock()
			currentConcurrency++
			fmt.Printf("任务 %d 开始，当前并发数: %d\n", taskID, currentConcurrency)
			mu.Unlock()

			// 模拟任务执行（耗时500ms）
			select {
			case <-ctx.Done():
				return ctx.Err()
			default:
				time.Sleep(500 * time.Millisecond)
			}

			// 3. 任务结束：并发数-1
			mu.Lock()
			currentConcurrency--
			fmt.Printf("任务 %d 结束，当前并发数: %d\n", taskID, currentConcurrency)
			mu.Unlock()

			return nil
		})
	}

	// 等待所有任务完成
	if err := g.Wait(); err != nil {
		fmt.Printf("执行出错: %v\n", err)
	} else {
		fmt.Println("所有任务执行完毕")
	}
}

```

执行结果：
```shell
任务 2 开始，当前并发数: 1
任务 1 开始，当前并发数: 2
任务 1 结束，当前并发数: 1
任务 3 开始，当前并发数: 2
任务 2 结束，当前并发数: 1
任务 4 开始，当前并发数: 2
任务 4 结束，当前并发数: 1
任务 5 开始，当前并发数: 2
任务 3 结束，当前并发数: 1
任务 6 开始，当前并发数: 2
任务 6 结束，当前并发数: 1
任务 7 开始，当前并发数: 2
任务 5 结束，当前并发数: 1
任务 8 开始，当前并发数: 2
任务 8 结束，当前并发数: 1
任务 9 开始，当前并发数: 2
任务 7 结束，当前并发数: 1
任务 10 开始，当前并发数: 2
任务 9 结束，当前并发数: 1
任务 10 结束，当前并发数: 0
所有任务执行完毕
```


从执行结果看，并发数始终没有超过 2。

### 4. 尝试启动

`errgroup` 还提供了 `errgroup.TryGo` 可以**尝试启动一个任务**，它返回一个 `bool` 值，标识任务是否启动成功，`true` 表示成功，`false` 表示失败。

`errgroup.TryGo` 需要搭配 `errgroup.SetLimit` 一同使用，因为如果不限制并发数量，那么 `errgroup.TryGo` 始终返回 `true`，当达到最大并发数量限制时，`errgroup.TryGo` 返回 `false`。

示例如下：
```golang
package main

import (
	"fmt"
	"time"

	"golang.org/x/sync/errgroup"
)

func main() {
	// 创建一个 errgroup.Group
	var g errgroup.Group
	// 设置最大并发限制为 3
	g.SetLimit(3)

	// 启动 10 个 goroutine
	for i := 1; i <= 10; i++ {
		// 捕获当前循环变量i（避免闭包延迟绑定导致的序号错乱）
		num := i
		if g.TryGo(func() error {
			// 打印正在运行的 goroutine
			fmt.Printf("goroutine %d 正在启动\n", num)
			time.Sleep(2 * time.Second) // 模拟工作
			fmt.Printf("goroutine %d 已完成\n", num)
			return nil
		}) {
			// 如果成功启动，打印提示
			fmt.Printf("goroutine %d 启动成功\n", num)
		} else {
			// 如果达到并发限制，打印提示
			fmt.Printf("goroutine %d 无法启动（已达并发限制）\n", num)
		}
	}

	// 等待所有 goroutine 完成
	if err := g.Wait(); err != nil {
		fmt.Printf("遇到错误：%v\n", err)
	}

	fmt.Println("所有goroutine已完成。")
}

```

执行结果：
```shell
goroutine 1 启动成功
goroutine 1 正在启动
goroutine 2 启动成功
goroutine 3 启动成功
goroutine 4 无法启动（已达并发限制）
goroutine 5 无法启动（已达并发限制）
goroutine 6 无法启动（已达并发限制）
goroutine 7 无法启动（已达并发限制）
goroutine 8 无法启动（已达并发限制）
goroutine 9 无法启动（已达并发限制）
goroutine 10 无法启动（已达并发限制）
goroutine 3 正在启动
goroutine 2 正在启动
goroutine 2 已完成
goroutine 1 已完成
goroutine 3 已完成
所有goroutine已完成。
```

# 参考
- [https://github.com/golang/sync](https://github.com/golang/sync)
- [mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA](https://mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA)
- [Go 并发控制：errgroup 详解-CSDN博客](https://blog.csdn.net/ra681t58cjxsgckj31/article/details/143616687)
- [Go 并发控制：errgroup 详解 \| 江湖十年 \| 学而不思则罔，思而不学则殆。](https://jianghushinian.cn/2024/11/04/x-sync-errgroup/)
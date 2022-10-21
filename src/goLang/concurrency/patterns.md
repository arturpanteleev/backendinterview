## Concurrency patterns

## Pipeline(конвеер)

Каналы могут использоваться для подключения go-подпрограмм так, чтобы выход одной из них был входом для другой. Это называется конвейером (pipeline).

```go
func main() {
	naturals := make(chan int)
	squares := make(chan int)
    // Генерация
    go func() {
        for x := 0; ; x++ {
            naturals <-x
        }
    }()
    // Возведение в квадрат
    go func() {
        for {
            x := <-naturals
            squares <- x * x
        }
    }()
    // Вывод (в главной go-подпрограмме)
    for {
    	fmt.Println(<-squares)
    }
}
```



## Broadcasting

В go нет возожности полноценной широковещательной рассылки нескольким получателям, однако есть лайфхак: при закрытии канала, все консюммеры, могут фиксировать это событие(закрытие канала)

## Cancelation

Чтобы отменить выполнение горутины в определенный момент, можем использовать канал отмены:

```go
func main() {
	var cancelChan chan bool
	cancelChan  = make(chan bool)

	go func() {
		for {
			select {
			case <-cancelChan:
				fmt.Println("Получили сигнал, отмены, вышли")
				return
			default:
				fmt.Println("Делаем полезную работу")
			}
			time.Sleep(3)

		}
	}()

	time.Sleep(10)
	cancelChan <- true
}
```

## Promises

В информатике конструкции future, promise и delay в некоторых языках программирования формируют стратегию вычисления, применяемую для параллельных вычислений. С их помощью описывается объект, к которому можно обратиться за результатом, вычисление которого может быть не завершено на данный момент. Использование future может быть неявным (при любом обращении к future возвращается ссылка на значение) и явным (пользователь должен вызвать функцию, чтобы получить значение). Получение значения из явного future называют stinging или forcing. Явные future могут быть реализованы в качестве библиотеки, в то время, как неявные обычно реализуются как часть языка.

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

type Promise struct {
	wg  sync.WaitGroup
	res string
	err error
}

func NewPromise(f func() (string, error)) *Promise {
	p := &Promise{}
	p.wg.Add(1)
	go func() {
		p.res, p.err = f()
		p.wg.Done()
	}()
	return p
}

func (p *Promise) Then(r func(string), e func(error)) {
	go func() {
		p.wg.Wait()
		if p.err != nil {
			e(p.err)
			return
		}
		r(p.res)
	}()
}

func exampleTicker() (string, error) {
	<-time.Tick(time.Second * 1)
	return "hi", nil
}

func main() {
	doneChan := make(chan int)
	var p = NewPromise(exampleTicker)
	p.Then(func(result string) { fmt.Println(result); doneChan <- 1 }, func(err error) { fmt.Println(err) })
	<-doneChan
}

```

### Generator aka Iterator

### Futures/Promises

### Fan-in, Fan-out
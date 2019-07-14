# Сoncurrency

## Горутины

Горутина — это функция, которая может работать параллельно с другими функциями. Для создания горутины используется ключевое слово `go`, за которым следует вызов функции. Горутины очень легкие(примерно 4.5кб на горутину против нескольких мегобайт на поток POSIX ).

### Отличия горутин от потоков:

- Каждый поток операционной системы имеет блок памяти фиксированного разме­ра (зачастую до 2 Мбайт) для стека — рабочей области, в которой он хранит локальные переменные вызовов функций, находящиеся в работе или приостановленные на время вызова другой функции. В противоположность этому go-подпрограмма начинает работу с небольшим стеком, обычно около 2 Кбайт. Стек go-подпрограммы, подобно стеку потока опе­рационной системы, хранит локальные переменные активных и приостановленных функций, но, в отличие от потоков операционной системы, не является фиксирован­ным; при необходимости он может расти и уменьшаться. Максимальный размер стека
  go-подпрограммы может быть около 1 Гбайта, на порядки больше типичного стека с фиксированным размером, хотя, конечно, такой большой стек могут использовать только несколько go-подпрограмм.
- Потоки операционной системы планируются в ее ядре, а  у go есть собственный планировщик (m:n) мультиплексирующий(раскидывающий) горутинки(m) по потокам(n). Основной плюс = отсуствие оверхеда на переключение контекста.
- Планировщик Go использует параметр с именем GOMAXPROCS для определения, сколько потоков операционной системы могут одновременно активно выполнять код Go. Его значение по умолчанию равно количеству процессоров компьютера, так что на машине с 8 процессорами планировщик будет планировать код Go для выполне­ния на 8 потоках одновременно (GOMAXPROCS равно значению п в т:п-планировании).
  Спящие или заблокированные в процессе коммуникации go-подпрограммы потоков для себя не требуют. Go-подпрограммы, заблокированные в операции ввода-вывода или в других системных вызовах, или при вызове функций, не являющихся функция­ми Go, нуждаются в потоке операционной системы, но GOMAXPROCS их не учитывает.
- В большинстве операционных систем и языков программирования, поддержива­ющих многопоточность, текущий поток имеет идентификацию, которая может быть легко получена как обычное значение (обычно — целое число или указатель). Это облегчает построение абстракции, именуемой локальной памятью потока, которая, по существу, является глобальным отображением, использующим в качестве ключа идентификатор потока, так что каждый поток может сохранять и извлекать значения независимо от других потоков.
  У go-подпрограмм нет понятия идентификации, доступной программисту. Так решено во время проектирования языка, поскольку локальной памятью потока про­граммисты, как правило, злоупотребляют.

## Каналы

В Go реализована поддержка concurrency. Механизмы реализации concurrency Go основаны на работе Тони Хоара (Tony Hoare) [‘Communicating Sequential Processes’](http://www.cs.cmu.edu/~crary/819-f09/Hoare78.pdf).

**Среда исполнения Go позволяет выполнять сотни тысяч конкурентных горутин на одной машине.** Горутина — это легковесный поток выполнения. Go умеет мультиплексировать горутины в потоках ОС. То есть несколько горутин могут конкурентно выполняться в одном потоке операционной системы. В среде исполнения Go предусмотрен специальный планировщик, в задачу которого входит управление выполнением горутин.

У такого подхода есть два преимущества:

1. При инициализации горутина занимает в стеке лишь 4 KB. Это мизер по сравнению, например, с 1 мегабайтом, который обычно выделяется потоку операционной системы. Это число обретает значимость при необходимости конкурентного выполнения сотен тысяч различных горутин на одной машине. Если для этих целей использовать потоки ОС, оперативная память может очень быстро закончиться.
2. Go мог пойти по тому же пути, что и другие языки, например Java, в которой используются потоки операционной системы. Однако в этом случае цена переключения контекста между потоками ОС гораздо выше по сравнению с ценой переключения контекста между различными горутинами.

## Каналы

Каналы (channels) представляют инструменты коммуникации между горутинами. Для определения канала применяется ключевое слово chan:

```go
var intCh chan int
```



```go
type hchan struct {
	qcount   uint           // total data in the queue
	dataqsiz uint           // size of the circular queue
	buf      unsafe.Pointer // points to an array of dataqsiz elements
	elemsize uint16
	closed   uint32
	elemtype *_type // element type
	sendx    uint   // send index
	recvx    uint   // receive index
	recvq    waitq  // list of recv waiters
	sendq    waitq  // list of send waiters

	// lock protects all fields in hchan, as well as several
	// fields in sudogs blocked on this channel.
	//
	// Do not change another G's status while holding this lock
	// (in particular, do not ready a G), as this can deadlock
	// with stack shrinking.
	lock mutex
}
```

В общем случае, горутина захватывает мьютекс, когда совершает какое-либо действие с каналом, кроме случаев lock-free проверок при неблокирующих вызовах (я объясню это подробнее чуть ниже). Closed — это флаг, который устанавливается в 1, если канал закрыт, и в 0, если не закрыт. Эти поля далее будут исключены из общей картины, для большей ясности.

Канал может быть синхронным (небуферизированным) или асинхронным (буферезированным). Давайте вначале посмотрим, как работают синхронные каналы.

### Синхронные(небуферизированные) каналы

```go
package main

func main() {
    ch := make(chan bool)
    go func() {
        ch <- true
    }()
    <-ch
}
```

Вначале создается новый канал и он выглядит вот так:

![img](https://habrastorage.org/files/725/298/c69/725298c69c2b4319b3cb5cf606712124.png)

Go не выделяет буфер для синхронных каналов, поэтому указатель на буфер равен nil и `dataqsiz`равен нулю. В приведённом коде нет гарантии, что случится первее — чтение из канала или запись, поэтому допустим, что первым действием будет чтение из канала (обратный пример, когда вначале идёт запись, будет рассмотрена ниже в примере с буферизированным каналами). Вначале, текущая горутина произведёт некоторые проверки, такие как: закрыт ли канал, буферизирован он или нет, содержит ли гоуртины в send-очереди. В нашем примере у канала нет ни буфера, ни ожидающих отправки горутин, поэтому горутина добавит сама себя в `recvq` и заблокируется. На этом шаге наш канал будет выглядеть следующим образом:

![img](https://habrastorage.org/files/2af/b2c/796/2afb2c79621847e0a28f118f92ed5c10.png)

Теперь у нас осталась только одна работающая горутина, которая пытается записать данные в канал. Все проверки повторяются снова, и когда горутина проверяет `recvq` очередь, она находит ожидающую чтение горутину, удаляет её из очереди, записывает данные в её стек и снимает блокировку. Это единственное место во всём рантайме Go, когда одна горутина пишет напрямую в стек другой горутины. После этого шага, канал выглядит точно так же, как сразу после инициализации. Обе горутины завершаются и программа выходит.

### Буферезированные каналы

Рассмотрим следующий пример:

```go
package main

func main() {
    ch := make(chan bool, 1)
    ch <- true
    go func() {
        <-ch
    }()
    ch <- true
}
```

Опять же, порядок исполнения неизвестен, пример с первой читающей горутиной мы разобрали выше, поэтому сейчас допустим, что два значения были записаны в канал, и после этого один из элементов вычитан. И первым шагом идёт создание канала, который будет выглядеть вот так:

![img](https://habrastorage.org/files/6f4/e78/6af/6f4e786af89b47f49e0b52d1869a2180.png)

Разница в сравнении с синхронным каналом в том, что тут Go выделяет буфер и устанавливает значение `dataqsiz` в единицу.

Следующим шагом будет отправка первого значения в канал. Чтобы сделать это, горутина сначала производит несколько проверок: пуста ли очередь `recvq`, пуст ли буфер, достаточно ли места в буфере.

В нашем случае в буфере достаточно места и в очереди ожидания чтения нет горутин, поэтому горутина просто записывает элемент в буфер, увеличивает значение `qcount` и продолжает исполнение далее. Канал в этот момент выглядит так:

![img](https://habrastorage.org/files/40c/f56/e00/40cf56e008e44b21ab2bbf7a5afe8a5e.png)

На следующем шаге, горутина main отправляет следующее значение в канал. Когда буфер полон, буферизированный канал будет вести себя точно так же, как синхронный (небуферизированный) канал, тоесть горутина добавит себя в очередь ожидания и **заблокируется**, в результате чего, канал будет выглядеть следующим образом:

![img](https://habrastorage.org/files/279/503/8c4/2795038c432c4ff38041e67086cb4e56.png)

Сейчас горутина main заблокирована и Go запустил одну анонимную горутину, которая пытается прочесть значение из канала. И вот тут начинается хитрая часть. Go гарантирует, что канал работает по принципу FIFO очереди ([спецификация](https://golang.org/ref/spec#Channel_types)), но горутина не может просто взять значение из буфера и продолжить исполнение. В этом случае горутина main заблокируется навсегда. Для решения этой ситуации, текущая горутина читает данные из буфера, затем добавляет значение из заблокированной горутины в буфер, разблокирует ожидающую горутину и удаляет её из очереди ожидания. (В случае же, если нет ожидающих горутину, она просто читает первое значение из буфера)

### Select

Но постойте, Go же ещё поддерживает select с дефолтным поведением, и если канал заблокирован, как горутина сможет обработать default? Хороший вопрос, давайте быстро посмотрим на приватное API каналов. Когда вы запускаете следующий кусок кода:

```go
select {
    case <-ch:
    foo()
    default:
    bar()
}
```

Go запускает функцию со следующей сигнатурой:

```go
func chanrecv(t *chantype, c *hchan, ep unsafe.Pointer, block bool)
```

`chantype` это тип канала (например, bool в случае make(chan bool)), `hchan` — указатель на структуру канала, `ep` — указатель на сегмент памяти, куда должны быть записаны данные из канала, и последний, но самый интересный для нас — это аргумент `block`. Если он установлен в `false`, то функция будет работать в неблокирующем режиме. В этом режиме горутина проверяет буфер и очередь, возвращает `true` и пишет данные в `ep` или возвращает `false`, если нет данных в буфере или нет отправителей в очереди. Проверки буфера и очереди реализованы как атомарные операции, и не требуют блокировки мьютекса.

### Закрытие канала

Закрытие канала это простая операция. Go проходит по всем ожидающим на чтение или запись горутинам и разблокирует их. Все получатели получают дефолтные значение переменных того типа данных канала, а все отправители паникуют.

## Пакет sync

### Atomic

Пакет Atomic предоставляет низкоуровневые атомарные примитивы памяти, полезные для реализации алгоритмов синхронизации. Пример:


```go
var ops uint64 = 0
for i := 0; i < 50; i++ {
    go func() {
        for {
            atomic.AddUint64(&ops, 1)
            runtime.Gosched()
        }
    }()
}
```

## Мьютексы

```go
// A Mutex is a mutual exclusion lock.
// The zero value for a Mutex is an unlocked mutex.
//
// A Mutex must not be copied after first use.
type Mutex struct {
	state int32
	sema  uint32
}
```

Для упрощения синхронизации между горутинами в Go имеется пакет **sync**, который предоставляет ряд возможностей, в частности мьютексы. Мьютексы позволяют разграничить доступ к некоторым общим ресурсам, гарантируя, что только одна горутина имеет к ним доступ в определенный момент времени. И пока одна горутина не освободит общий ресурс, другая горутина не может с ним работать.

### sync.Mutex

На уровне кода мьютекс представляет тип **sync.Mutex**. Для блокирования доступа к общему разделяемому ресурсу у мьютекса вызывается метод **Lock()**, а для разблокировки доступа - метод **Unlock()**.

### sync.RWMutex

Существует блокировка `несколько читателей, один писатель` и в Go обеспечивается мьютексом **sync. RWMutex** При его использовании чтение может спокойно выполняться параллельно, но операции записи получают, полностью эксклюзивный доступ.

```go
// A RWMutex is a reader/writer mutual exclusion lock.
// The lock can be held by an arbitrary number of readers or a single writer.
// The zero value for a RWMutex is an unlocked mutex.
//
// A RWMutex must not be copied after first use.
//
// If a goroutine holds a RWMutex for reading and another goroutine might
// call Lock, no goroutine should expect to be able to acquire a read lock
// until the initial read lock is released. In particular, this prohibits
// recursive read locking. This is to ensure that the lock eventually becomes
// available; a blocked Lock call excludes new readers from acquiring the
// lock.
type RWMutex struct {
	w           Mutex  // held if there are pending writers
	writerSem   uint32 // semaphore for writers to wait for completing readers
	readerSem   uint32 // semaphore for readers to wait for completing writers
	readerCount int32  // number of pending readers
	readerWait  int32  // number of departing readers
}
```

```go
var mu sync.RWMutex
var balance int

func BalanceQ int { 
	mu.RLockQ // Блокировка читателя
	defer mu.RUnlock()
	return balance
}
```

### sync.Once

Нужен

```go
// Once is an object that will perform exactly one action.
type Once struct {
	// done indicates whether the action has been performed.
	// It is first in the struct because it is used in the hot path.
	// The hot path is inlined at every call site.
	// Placing done first allows more compact instructions on some architectures (amd64/x86),
	// and fewer instructions (to calculate offset) on other architectures.
	done uint32
	m    Mutex
} 

// Do calls the function f if and only if Do is being called for the
// first time for this instance of Once. In other words, given
// 	var once Once
// if once.Do(f) is called multiple times, only the first call will invoke f,
// even if f has a different value in each invocation. A new instance of
// Once is required for each function to execute.
//
// Do is intended for initialization that must be run exactly once. Since f
// is niladic, it may be necessary to use a function literal to capture the
// arguments to a function to be invoked by Do:
// 	config.once.Do(func() { config.init(filename) })
//
// Because no call to Do returns until the one call to f returns, if f causes
// Do to be called, it will deadlock.
//
// If f panics, Do considers it to have returned; future calls of Do return
// without calling f.
//
func (o *Once) Do(f func()) {
	// Note: Here is an incorrect implementation of Do:
	//
	//	if atomic.CompareAndSwapUint32(&o.done, 0, 1) {
	//		f()
	//	}
	//
	// Do guarantees that when it returns, f has finished.
	// This implementation would not implement that guarantee:
	// given two simultaneous calls, the winner of the cas would
	// call f, and the second would return immediately, without
	// waiting for the first's call to f to complete.
	// This is why the slow path falls back to a mutex, and why
	// the atomic.StoreUint32 must be delayed until after f returns.

	if atomic.LoadUint32(&o.done) == 0 {
		// Outlined slow-path to allow inlining of the fast-path.
		o.doSlow(f)
	}
}
```

### sync.Map

`Если у вас высоконагруженная (и 100нс решают) система с большим количеством ядер процессора (32+), вы можете захотеть использовать sync.Map вместо стандартного map+sync.RWMutex. В остальных случаях, sync.Map особо не нужен`

Итак, ещё раз сделаю акцент — sync.Map решает совершенно конкретную проблему cache contention в стандартной библиотеке для таких случаев, когда ключи в map стабильны (не обновляются часто) и происходит намного больше чтений, чем записей.

**Если вы совершенно чётко не идентифицировали в своей программе узкое место из-за cache contention в map+RWMutex, то, вероятнее всего, никакой выгоды от sync.Map вы не получите, и возможно даже слегка потеряете в скорости.**

### sync.Pool

Сборщик мусора (далее GC) не постоянно собирает мусор, а через определённые промежутки времени. В случае если ваш код выделяет память под некоторые структуры данных, а потом освобождает их — и так по кругу — это вызывает определённое давление на GC, в том числе заставляет `runtime`*кусок*`[]byte`*кусок**кусок**кусок*

- создать пул
- сбрасывать состояние *куска*
- складывать в пул отработанные *куски*
- брать новые *куски* из пула

A Pool is a set of temporary objects that may be individually saved and retrieved.

Any item stored in the Pool may be removed automatically at any time without notification. If the Pool holds the only reference when this happens, the item might be deallocated.

A Pool is safe for use by multiple goroutines simultaneously.

Pool's purpose is to cache allocated but unused items for later reuse, relieving pressure on the garbage collector. That is, it makes it easy to build efficient, thread-safe free lists. However, it is not suitable for all free lists.

An appropriate use of a Pool is to manage a group of temporary items silently shared among and potentially reused by concurrent independent clients of a package. Pool provides a way to amortize allocation overhead across many clients.

An example of good use of a Pool is in the fmt package, which maintains a dynamically-sized store of temporary output buffers. The store scales under load (when many goroutines are actively printing) and shrinks when quiescent.

On the other hand, a free list maintained as part of a short-lived object is not a suitable use for a Pool, since the overhead does not amortize well in that scenario. It is more efficient to have such objects implement their own free list.

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
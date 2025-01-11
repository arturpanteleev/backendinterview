## Sync

### Atomic

Пакет **Atomic** предоставляет низкоуровневые **атомарные** примитивы памяти, полезные для реализации алгоритмов синхронизации. Самый популярный кейс использования, это инкрементальный счётчик. Можно использовать и не бояться, что из за других горутин произойдет **dirty read** или **lost update**

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

Типичные функции:

* Add
* Load
* Store
* CompareAndSwap

### sync.Mutex

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

### sync.WaitGroup

Этот тип позволяет определить группу горутин, которые должны выполняться вместе как одна группа. И можно установить блокировку, которая приостановит выполнение функции, пока не завершит выполнение вся группа горутин. 

Обратите внимание, что передавать `WaitGroup` в горутину, необходимо по ссылке. Так как если передать по значению, то она скопируется, и горутины будут выполнять `wg.Done()` для другой `WaitGroup` и "родительский" `WaitGroup` никогда не получит вызовы `wg.Done()` и  программа зависнет на `wg.Wait()

```go
func main() { 
    var wg sync.WaitGroup 
    wg.Add(2)       // в группе две горутины
    counter := 5
    doubleCounter := func() { 
        defer wg.Done() 
        counter = counter * 2
   } 

   // вызываем горутины
   go doubleCounter() 
   go doubleCounter() 

   wg.Wait()        // ожидаем завершения обоих горутин
   fmt.Println("Counter:", counter) 
}
```

Также можно использовать WaitGroup через замыкание, посколько в этом случае, переменная  `wg` будет скопирована по указателю, и работа будет осуществляться с одним и тем же объектом.

```go
func main() {
	var wg sync.WaitGroup

	wg.Add(1)
	go func() {
		defer wg.Done() // Замыкание захватывает переменную по ссылке
		fmt.Println("Inside goroutine")
	}()

	wg.Wait() // Работает как ожидается
	fmt.Println("Done")
}
```



### sync.Once

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
//     var once Once
// if once.Do(f) is called multiple times, only the first call will invoke f,
// even if f has a different value in each invocation. A new instance of
// Once is required for each function to execute.
//
// Do is intended for initialization that must be run exactly once. Since f
// is niladic, it may be necessary to use a function literal to capture the
// arguments to a function to be invoked by Do:
//     config.once.Do(func() { config.init(filename) })
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
    //    if atomic.CompareAndSwapUint32(&o.done, 0, 1) {
    //        f()
    //    }
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

Если у вас **высоконагруженная (и 100нс решают) система с большим количеством ядер процессора (32+) + ключи в map стабильны(обновляются редко) и происходит намного больше чтений, чем записей**, вы можете захотеть использовать sync.Map вместо стандартного map+sync.RWMutex. В остальных случаях, sync.Map особо не нужен

Если вы совершенно чётко не идентифицировали в своей программе узкое место из-за **cache contention ** в map+RWMutex, то, вероятнее всего, никакой выгоды от sync.Map вы не получите, и даже потеряете в скорости.

**Cache contention**

Если посмотреть на [код sync.RWMutex](https://golang.org/src/sync/rwmutex.go#L94), то можно увидеть, что при блокировке на чтение, каждая горутина должна обновить поле `readerCount` — простой счётчик. Это делается атомарно с помощью функции из пакета [sync/atomic](https://golang.org/pkg/sync/atomic) [atomic.AddInt32()](https://golang.org/pkg/sync/atomic/#AddInt32). Эти функции оптимизированы под архитектуру конкретного процессора и [реализованы на ассемблере](https://github.com/golang/go/blob/master/src/sync/atomic/asm_amd64.s#L65).

Когда каждое ядро процессора обновляет счётчик, оно сбрасывает кеш для этого адреса в памяти для всех остальных ядер и объявляет, что владеет актуальным значением для этого адреса. Следующее ядро, прежде чем обновить счётчик, должно сначала вычитать это значение из кеша другого ядра.

На современном железе передача между L2 кешем занимает что-то около 40 наносекунд. Это немного, но когда много ядер одновременно пытаются обновить счётчик, каждое из них становится в очередь и ждёт эту инвалидацию и вычитывание из кеша. Операция, которая должна укладываться в константное время внезапно становится O(N) по количеству ядер. Эта проблема называется **cache contention**.

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

##sync.Cond
Cond implements a condition variable, a rendezvous point for goroutines waiting for or announcing the occurrence of an event. In the terminology of the Go memory model, Cond arranges that a call to Broadcast or Signal “synchronizes before” any Wait call that it unblocks.

For many simple use cases, users will be better off using channels than a Cond (Broadcast corresponds to closing a channel, and Signal corresponds to sending on a channel).

**sync.Cond** позволяет синхронизироваться горутинам по событиям, заменить бесконечный цикл ожидания какого-нибудь определенного условия. Это полезно, например, если мы хотим разблокировать сразу несколько горутин (Broadcast), что не получится сделать с помощью канала

##sync.Locker
A Locker represents an object that can be locked and unlocked.

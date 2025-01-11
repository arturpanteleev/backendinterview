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

`sync.Once` в Go — это структура из пакета `sync`, которая гарантирует, что определенный код выполнится только один раз, даже если из разных потоков или горутин к нему поступят конкурентные вызовы. Используется для инициализации ресурсов, которые должны быть созданы только один раз за весь жизненный цикл программы. Это удобно для ленивой инициализации, однократной настройки глобальных переменных или загрузки конфигураций.

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

Пример:

```go
package main

import (
	"fmt"
	"sync"
)

var once sync.Once
var config string

func loadConfig() {
	fmt.Println("Loading config...")
	config = "App Configuration"
}

func main() {
	for i := 0; i < 3; i++ {
		go func() {
			once.Do(loadConfig)
			fmt.Println(config)
		}()
	}

	// Небольшая задержка, чтобы горутины успели выполниться
	fmt.Scanln()
}
```



### sync.Map

Если у вас **высоконагруженная (и 100нс решают) система с большим количеством ядер процессора (32+) + ключи в map стабильны и происходит намного больше чтений, чем записей**, вы можете захотеть использовать `sync.Map` вместо стандартного `map+sync.RWMutex`. 

Если вы совершенно чётко не идентифицировали в своей программе узкое место из-за ***cache contention** в `map+RWMutex` то, вероятнее всего, никакой выгоды от `sync.Map` вы не получите, и даже потеряете в скорости.

**Cache contention**

Если посмотреть на [код sync.RWMutex](https://golang.org/src/sync/rwmutex.go#L94), то можно увидеть, что при блокировке на чтение, каждая горутина должна обновить поле `readerCount` — простой счётчик. Это делается атомарно с помощью функции из пакета [sync/atomic](https://golang.org/pkg/sync/atomic) [atomic.AddInt32()](https://golang.org/pkg/sync/atomic/#AddInt32). Эти функции оптимизированы под архитектуру конкретного процессора и [реализованы на ассемблере](https://github.com/golang/go/blob/master/src/sync/atomic/asm_amd64.s#L65).

Когда каждое ядро процессора обновляет счётчик, оно сбрасывает кеш для этого адреса в памяти для всех остальных ядер и объявляет, что владеет актуальным значением для этого адреса. Следующее ядро, прежде чем обновить счётчик, должно сначала вычитать это значение из кеша другого ядра.

На современном железе передача между L2 кешем занимает что-то около 40 наносекунд. Это немного, но когда много ядер одновременно пытаются обновить счётчик, каждое из них становится в очередь и ждёт эту инвалидацию и вычитывание из кеша. Операция, которая должна укладываться в константное время внезапно становится `O(N) `по количеству ядер. Эта проблема называется **cache contention**.

### sync.Pool

`sync.Pool` — это структура в Go из пакета `sync`, предназначенная для управления временными объектами и их переиспользования, чтобы снизить нагрузку на сборщик мусора (Garbage Collector, GC) и улучшить производительность.

Сборщик мусора (далее GC) не постоянно собирает мусор, а через определённые промежутки времени. В случае если ваш код выделяет память под некоторые структуры данных, а потом освобождает их — и так по кругу — это вызывает определённое давление на GC

####`sync.Pool` — это структура в Go из пакета `sync`, предназначенная для управления временными объектами и их переиспользования, чтобы снизить нагрузку на сборщик мусора (Garbage Collector, GC) и улучшить производительность.

---

### Зачем нужен `sync.Pool`?

В высоконагруженных приложениях частое создание и удаление объектов приводит к:

1. Повышенной нагрузке на сборщик мусора.
2. Увеличению времени работы программы из-за затрат на выделение и освобождение памяти.

`sync.Pool` помогает решить эти проблемы, предоставляя возможность переиспользовать ранее созданные объекты вместо их удаления и повторного создания.

---

### Как работает `sync.Pool`?

1. **Хранилище объектов:**
   
   - Объекты помещаются в пул с помощью метода `Put`.
   - Извлекаются из пула с помощью метода `Get`.

2. **Автоматическое создание объекта:**
   
   - Если `Get` вызывается, а пул пуст, создается новый объект с помощью функции `New`, заданной при создании пула.

3. **Особенность:**
   
   - Объекты в пуле могут быть удалены сборщиком мусора, если они не используются, поэтому `sync.Pool` не гарантирует сохранение всех объектов.

---

### Примеры использования

#### Пример 1: Использование для буферов

Сценарий: обработка HTTP-запросов, где часто создаются временные буферы.

go

Copy code

`package main  import (     "bytes"     "fmt"     "sync" )  var bufferPool = sync.Pool{     New: func() interface{} {         return new(bytes.Buffer)     }, }  func main() {     // Получаем буфер из пула     buf := bufferPool.Get().(*bytes.Buffer)     defer bufferPool.Put(buf) // Возвращаем буфер в пул      buf.WriteString("Hello, sync.Pool!")     fmt.Println(buf.String())      // Сброс буфера перед возвратом в пул     buf.Reset() }`

**Преимущество:** Вместо создания нового `bytes.Buffer` каждый раз, мы берем его из пула, уменьшая нагрузку на сборщик мусора.

---

#### Пример 2: Работа с временными структурами

Сценарий: парсинг большого количества JSON, где временно создаются объекты.

go

Copy code

``package main  import (     "encoding/json"     "fmt"     "sync" )  type Data struct {     ID   int     Name string }  var dataPool = sync.Pool{     New: func() interface{} {         return &Data{}     }, }  func main() {     rawJSON := `{"ID": 1, "Name": "Alice"}`     data := dataPool.Get().(*Data)     defer dataPool.Put(data)      // Парсинг JSON     if err := json.Unmarshal([]byte(rawJSON), data); err != nil {         fmt.Println("Error:", err)         return     }     fmt.Printf("Parsed Data: %+v\n", data)      // Сброс данных перед возвратом в пул     *data = Data{} }``

**Преимущество:** Мы избегаем постоянного выделения памяти для новых объектов `Data`.

---

#### Пример 3: Горутинный пул

Сценарий: в высоконагруженном приложении требуется часто запускать одинаковые задачи.

go

Copy code

`package main  import (     "fmt"     "sync" )  var taskPool = sync.Pool{     New: func() interface{} {         return func(data int) {             fmt.Println("Processing:", data)         }     }, }  func main() {     var wg sync.WaitGroup     for i := 0; i < 5; i++ {         wg.Add(1)         go func(i int) {             defer wg.Done()              task := taskPool.Get().(func(int))             task(i)              taskPool.Put(task) // Возвращаем функцию в пул         }(i)     }      wg.Wait() }`

**Преимущество:** Переиспользование задач для снижения затрат на выделение памяти.

---

#### Особенности и ограничения

1. **Не гарантирует хранения объектов:** Если объект из пула не используется, он может быть собран сборщиком мусора.Это делает `sync.Pool` полезным для временных объектов, а не для долгосрочного хранения.

2. **Идеален для короткоживущих объектов:** Например, буферы, временные структуры или объекты, используемые в одной итерации цикла.

3. **Не стоит использовать для больших объектов:** Большие объекты могут увеличить нагрузку на память, если их часто помещают в пул.

4. `sync.Pool` оптимизирован для многопоточного использования, поэтому можно безопасно работать с ним в горутинах.

Пример:

```go
var dataPool = sync.Pool{New: func() interface{} { return &Data{} }}
data := dataPool.Get().(*Data)
*data = Data{}
dataPool.Put(data)
```



### sync.Cond

`sync.Cond` — это синхронизационная примитивная структура из пакета `sync`, предназначенная для реализации условной синхронизации между горутинами. Она позволяет одной или нескольким горутинам **ожидать наступления события** и уведомлять другие горутины, когда это событие произошло.

### `sync.Cond` в Go

`sync.Cond` — это синхронизационная примитивная структура из пакета `sync`, предназначенная для реализации условной синхронизации между горутинами. Она позволяет одной или нескольким горутинам **ожидать наступления события** и уведомлять другие горутины, когда это событие произошло.

---

#### Основные методы

1. **`Wait()`**
   
   - Блокирует текущую горутину, пока не будет вызван метод `Signal` или `Broadcast`.
   - Перед вызовом `Wait()` необходимо заблокировать связанный `Locker` (например, `sync.Mutex`), иначе возникнет паника.

2. **`Signal()`**
   
   - Уведомляет одну из горутин, ожидающих условия (если такие есть), чтобы она продолжила выполнение.

3. **`Broadcast()`**
   
   - Уведомляет **все горутины**, ожидающие условия, чтобы они продолжили выполнение.

Если вы можете реализовать задачу с помощью каналов, это обычно предпочтительнее из-за упрощения кода и меньшей вероятности ошибок.

### sync.Locker

`sync.Locker` — это интерфейс в Go, который представляет собой абстракцию для объектов синхронизации, обеспечивающих блокировку. Этот интерфейс используется для обеспечения потокобезопасности в многозадачных приложениях, позволяя контролировать доступ к критическим секциям кода, где происходят операции с разделяемыми ресурсами.

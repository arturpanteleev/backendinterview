# Map

В Go `map` — это встроенная хеш-таблица с открытой адресацией. Она обеспечивает быстрый доступ к данным с временной сложностью `O(1)` в среднем случае. Каждый элемент имеет уникальный ключ, по которому можно получить значение элемента. В `map[K]V`, `К` представляет тип ключа, а `V` - тип значения. Тип ключа K должен быть comparable(поддерживать операцию сравнения `==`)

```go
// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/reflectdata/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}
```

![go_hash](../../media/go/go_hash.png)

Например, определение `map`, которое в качестве ключей имеет тип `string`, а в качестве значений - тип `int`:

```go
var people map[string] int   
```

Для проверки наличия элемента по определенному ключу можно получать два значения, второе и будет показывать наличие:

```go
    var people = map[string]int{"Tom": 1, "Bob": 2, "Sam": 4, "Alice": 8}

    if val, ok := people["Tom"]; ok {
        fmt.Println(val)
    }
```

Адрес элемента мапы получить невозможно(сделано из-за того что адрес может стать недействителен, после рехеширования)

```go
_ = &ages["bob"] // ТАК НЕЛЬЗЯ!!!
```

При чтении из nil мапы, ничего страшного не произойдёт, получим нулевое значение для типа. Но если писать в nil map то словим панику.

```go
    m1 := map[string]int{
        "key": 1,
    }
    m1 = nil 
    fmt.Println(m1["key"]) // 0
    m1["key"] = 1 //panic: assignment to entry in nil map
```



## **Бакеты (buckets)**

Данные в `map` хранятся в **бакетах**. Бакет содержит до 8 ключей и значений:

```go
type bmap struct {
    tophash [8]uint8 // Первые 8 бит хеша ключей
    keys    [8]K     // Ключи
    values  [8]V     // Значения
    overflow *bmap   // Указатель на следующий бакет (если есть переполнение)
}
```

- **`tophash`** хранит первые 8 бит хеша ключа.
- **`keys`** и **`values`** — массивы ключей и значений.

## **Рехеширование (grow)**

Когда элементов становится **слишком много**(больше 6.5 в среднем), `map` **удваивает** число бакетов:

- Создаётся новый массив бакетов `2^(B+1)`.
- Ключи перераспределяются.
- Это происходит **постепенно** (incremental rehashing), чтобы избежать резкого скачка нагрузки.

Когда рехеширование запускается:

1. `count > 6.5 * 2^B` → **увеличение размера (`grow`)**.
2. `overflow > определённого порога` → **перемещение (`evacuation`)**.



## **Итерация по `map`**

```go
for k, v := range m {
    fmt.Println(k, v)
}
```

Итерация **не гарантирует порядок** — это связано с рандомизацией хеширования.



## Почему нельзя взять указатель на элемент `map`

Go запрещает брать адрес элемента `map`:

```
m := map[string]int{"Alice": 25}p := &m["Alice"] // Ошибка компиляции
```

Причина в том, что Go **динамически перераспределяет память** для `map`. Когда в `map` добавляются новые элементы, он может **перераспределить бакеты** в памяти, и старые адреса станут невалидными. Это могло бы приводить к трудноотлавливаемым багам, поэтому Go запрещает такую возможность на уровне компилятора.

## Внутреннее устройство

Map — это просто хеш-таблица. Данные организованы в массив бакетов. Каждый бакет содержит до 8 пар "ключ-значение". Младшие биты хеша используются для выбора бакета. Каждый бакет хранит несколько старших битов хеша, чтобы различать записи внутри одного бакета.

Если в один бакет попадает более 8 ключей, создаются дополнительные (overflow) бакеты, образующие цепочку.

Когда хеш-таблица увеличивается, создаётся новый массив бакетов в два раза больше. Бакеты постепенно (инкрементально) копируются из старого массива в новый.

Итератор по map проходит по массиву бакетов и возвращает ключи в порядке обхода (сначала по номерам бакетов, затем по цепочкам overflow-бакетов, затем по индексам внутри бакета). Чтобы сохранить семантику итерации, ключи никогда не перемещаются внутри своего бакета (если бы они перемещались, один и тот же ключ мог бы быть возвращён 0 или 2 раза).

Во время увеличения таблицы итераторы продолжают обход старого массива и должны проверять новый массив, если бакет, по которому они идут, был перемещён ("эвакуирован") в новую таблицу.
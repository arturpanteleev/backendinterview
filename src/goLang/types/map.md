#  Map

Отображение или **map** представляет ссылку на хеш-таблицу - структуру данных, где каждый элемент представляет пару "ключ-значение". При этом каждый элемент имеет уникальный ключ, по которому можно получить значение элемента. Отображение определяется как объект типа map[K]V, где К представляет тип ключа, а V - тип значения. Причем тип ключа K должен поддерживать операцию сравнения ==, чтобы отображение могло сопоставить значение с одним из ключей и хеш-таблицы.

```go
// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/gc/reflect.go.
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

Например, определение отображения, которое в качестве ключей имеет тип string, а в качестве значений - тип int:

```go
var people map[string] int   
// Ключи представляют тип string, значения - тип int`
```

Для проверки наличия элемента по определенному ключу можно применять выражение if. Если значение по заданному ключу имеется в отображении, то переменная ok будет равна true, а переменная val будет хранить полученное значение. Если переменная ok равна false, то значения по ключу в отображении нет.

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

### 
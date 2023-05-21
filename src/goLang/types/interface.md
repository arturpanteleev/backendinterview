## Интерфейсы 

```go
type iface struct {
	tab  *itab
	data unsafe.Pointer
}

type itab struct {
	inter *interfacetype
	_type *_type
	hash  uint32 // copy of _type.hash. Used for type switches.
	_     [4]byte
	fun   [1]uintptr // variable sized. fun[0]==0 means _type does not implement inter.
}

type interfacetype struct {
	typ     _type
	pkgpath name
	mhdr    []imethod
} 

//пустой интерфейс interface{}
type eface struct {
    _type *_type
    data  unsafe.Pointer
}
```

Интерфейсы представляют абстракцию поведения других типов. Интерфейсы позволяют определять функции, которые не привязаны к конкретной реализации. То есть интерфейсы определяют некоторый функционал, но не реализуют его.

Для определения интерфейса применяется ключевое слово interface:

```go
type имя_интерфейса interface {определения_функций()}
```

### Nill interface vs nil pointer interface

<img src="../../media/go/nilIface.png" width="100%">
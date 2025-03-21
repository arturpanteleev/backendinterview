# Goroutines

Горутина — функция, которая может работать параллельно с другими функциями. Для создания горутины используется ключевое слово `go`, за которым следует вызов функции. Горутины очень легкие(примерно 4.5кб на горутину против нескольких мегабайт на поток POSIX).

## Отличия горутин от потоков

### Stack

Каждый поток операционной системы имеет блок памяти фиксированного размера (зачастую до 2 Мбайт) для стека — рабочей области, в которой он хранит локальные переменные вызовов функций, находящиеся в работе или приостановленные на время вызова другой функции. В противоположность этому go-подпрограмма начинает работу с небольшим стеком, обычно около **2 Кбайт**. Стек go-подпрограммы, подобно стеку потока операционной системы, хранит локальные переменные активных и приостановленных функций, но, в отличие от потоков операционной системы, не является фиксированным; при необходимости он может расти и уменьшаться. Максимальный размер стека go-подпрограммы может быть около 1 Гбайта, на порядки больше типичного стека с фиксированным размером, хотя, конечно, такой большой стек могут использовать только несколько go-подпрограмм.
Раньше использовался **Segmented Stacks** потом переделали на  **Сontiguous Stacks**

#### Segmented Stacks

The 1.2 runtime uses segmented stacks, also known as split stacks.

In this approach, stacks are discontiguous and grow incrementally.

Each stack starts with a single segment. When the stack needs to grow, another segment is allocated and linked to the previous one, and so forth. Each stack is effectively a doubly linked list of one or more segments.

#### Сontiguous Stacks

The “hot split” were addressed in Go 1.3 by making the stacks contiguous.

Now when a stack needs to grow, instead of allocating a new segment the runtime will:

* create a new, somewhat larger stack
* copy the contents of the old stack to the new stack
* re-adjust every copied pointer to point to the new addresses
* destroy the old stack

### Scheduler

Потоки операционной системы планируются в ее ядре, а у go есть собственный **планировщик (m:n) мультиплексирующий(раскидывающий) горутинки(m) по потокам(n)**. Основной плюс = отсуствие оверхеда на переключение контекста. Т.к переключение тредов происходит **kernel space**, то переключение потока очень дорогое - нужно сохранить его стэк, сохранить регистры, файловы регистры и т.д Переключение горутин происходит в **user space** и гораздо быстрее и дешевле.

### Gomaxproc

Планировщик Go использует параметр с именем **GOMAXPROCS** для определения, сколько потоков операционной системы могут одновременно активно выполнять код Go. Его значение по умолчанию равно количеству процессоров компьютера, так что на машине с 8 процессорами планировщик будет планировать код Go для выполнения на 8 потоках одновременно (**GOMAXPROCS** равно значению п в т:п-планировании).
Спящие или заблокированные в процессе коммуникации go-подпрограммы потоков для себя не требуют. Go-подпрограммы, заблокированные в операции ввода-вывода или в других системных вызовах, или при вызове функций, не являющихся функциями Go, нуждаются в потоке операционной системы, но **GOMAXPROCS** их не учитывает.

### No identification

В большинстве операционных систем и языков программирования, поддерживающих многопоточность, текущий поток имеет идентификацию, которая может быть легко получена как обычное значение (обычно — целое число или указатель). Это облегчает построение абстракции, именуемой локальной памятью потока, которая, по существу, является глобальным отображением, использующим в качестве ключа идентификатор потока, так что каждый поток может сохранять и извлекать значения независимо от других потоков. **У горутин нет идентификации, доступной программисту.** Так решено во время проектирования языка, поскольку локальной памятью потока программисты злоупотребляют.

*Дополнительно:*

- [Contiguous stacks in Go](https://agis.io/post/contiguous-stacks-golang/)

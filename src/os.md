# Операционные системы и устройство ПК

## Многозадачность и параллельные вычисления

**Многозадачность**(multitasking) — свойство операционной системы или среды выполнения обеспечивать возможность параллельной (или псевдопараллельной)) обработки нескольких задач. Истинная многозадачность операционной системы возможна только в распределённых вычислительных системах.

Есть несколько разных понятий, связанных с областью параллельных вычислений.

### Concurrency

Concurrency - наиболее общий термин, который говорит, что одновременно выполняется более одной задачи. Например, вы можете одновременно смотреть телевизор и комментить фоточки в фейсбуке.  К сожалению, вменяемого русскоязычного термина я не знаю. Википедия говорит, что concurrent computing - это параллельные вычисления, но как тогда будет parallel computing по русски?

Конкурентное исполнение - это самый общий термин, который не говорит о том, каким образом эта конкурентность будет получена: путем приостановки некоторых вычислительных элементов и их переключение на другую задачу, путем действительно одновременного исполнения, путем делегации работы другим устройствам или еще как-то. Это не важно.

*Конкурентное исполнение* говорит о том, что за определенный промежуток времени будет решена более, чем одна задача.

### Параллельное исполнение

Параллельное исполнение (parallel computing) подразумевает наличие более одного вычислительного устройства (например, процессора), которые будут *одновременно* выполнять несколько задач.

Параллельное исполнение - это строгое подмножество конкурентного исполнения. Это значит, что на компьютере с одним процессором параллельное программирование - невозможно;)

### Многопоточность

Многопоточность - это один из способов реализации конкурентного исполнения путем выделения абстракции "рабочего потока" (worker thread).

Потоки "абстрагируют" от пользователя низкоуровневые детали и позволяют выполнять более чем одну работу "параллельно". Операционная система, среда исполнения или библиотека прячет подробности того, будет многопоточное исполнение конкурентным (когда потоков больше чем физических процессоров), или параллельным (когда число потоков меньше или равно числу процессоров и несколько задач физически выполняются одновременно).

### Асинхронное исполнение

Асинхронность (asynchrony) подразумевает, что операция может быть выполнена кем-то на стороне: удаленным веб-узлом, сервером или другим устройством за пределами текущего вычислительного устройства.

Основное свойство таких операций в том, что начало такой операции требует значительно меньшего времени, чем основная работа. Что позволяет выполнять множество асинхронных операций одновременно даже на устройстве с небольшим числом вычислительных устройств.

### CPU-bound и IO-Bound операции

Еще один важный момент, с точки зрения разработчика - разница между **CPU-bound** и **IO-bound** операциями. **CPU-Bound** операции нагружают вычислительные мощности текущего устройства, а **IO-Bound** позволяют выполнить задачу вне текущей железки.

Разница важна тем, что число одновременных операций зависит от того, к какой категории они относятся. Вполне нормально запустить параллельно сотни IO-Bound операций, и надеяться, что хватит ресурсов обработать все результаты. Запускать же параллельно слишком большое число CPU-bound операций (больше, чем число вычислительных устройств) бессмысленно.

*Дополнительно*:

- [Многопоточное vs асинхронное программирование](https://ru.stackoverflow.com/questions/445768/%D0%9C%D0%BD%D0%BE%D0%B3%D0%BE%D0%BF%D0%BE%D1%82%D0%BE%D1%87%D0%BD%D0%BE%D0%B5-vs-%D0%B0%D1%81%D0%B8%D0%BD%D1%85%D1%80%D0%BE%D0%BD%D0%BD%D0%BE%D0%B5-%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5)

## Процессы и потоки

Существует 2 типа многозадачности:

- *Процессная многозадачность* (основанная на процессах — одновременно выполняющихся программах). Здесь программа — наименьший элемент управляемого кода, которым может управлять планировщик операционной системы. Более известна большинству пользователей (работа в текстовом редакторе и прослушивание музыки).
- *Поточная многозадачность* (основанная на потоках). Наименьший элемент управляемого кода — поток (одна программа может выполнять 2 и более задачи одновременно).

**Процесс** — экземпляр программы во время выполнения, независимый объект, которому выделены системные ресурсы (например, процессорное время и память). Каждый процесс выполняется в отдельном адресном пространстве: один процесс не может получить доступ к переменным и структурам данных другого. Если процесс хочет получить доступ к чужим ресурсам, необходимо использовать межпроцессное взаимодействие. Это могут быть конвейеры, файлы, каналы связи между компьютерами и многое другое.

**Поток** использует то же самое пространства стека, что и процесс, а множество потоков совместно используют данные своих состояний. Как правило, каждый поток может работать (читать и писать) с одной и той же областью памяти, в отличие от процессов, которые не могут просто так получить доступ к памяти другого процесса. У каждого потока есть собственные регистры и собственный стек, но другие потоки могут их использовать.

В Linux (в ядре) понятие «threads» отсутствует. С точки зрения ядра все потоки — это полноценные, отдельные, процессы (просто с общим адресным пространством)

Поток — определенный способ выполнения процесса. Когда один поток изменяет ресурс процесса, это изменение сразу же становится видно другим потокам этого процесса. Программный поток (thread): в русскоязычной литературе часто употребляют термин «нить» (в программистском жаргоне — «нитка»).

Рассказ о жизни процессов естественно начать с самого начала - с их появления на свет. Так вот, процессы размножаются... почкованием: системный вызов Linux, создающий новый процесс, называется **clone**, а дочерний процесс представляет собой почти точную копию родительского. Только далее он выполняет назначенную ему функцию, а исходный процесс - то, что написано в программе после вызова clone. Потом отличий может стать больше, так что пути-дороги процессов способны разойтись достаточно далеко. Но если нам нужно этому воспрепятствовать, вызов clone позволит задать флаги, указывающие, что порожденный процесс будет иметь со своим предком общие моменты.

Если родительский процесс по какой-то причине завершится раньше дочернего, последний становится **сиротой (orphaned process)**. "Сироты" автоматически "усыновляются" программой init, выполняющейся в процессе с номером 1, которая и принимает сигнал об их завершении.

Если же потомок уже завершил работу, а предок не готов принять от системы сигнал об этом событии, то потомок не исчезает полностью, а превращается в **зомби (zombie)**; в поле Stat такие процессы помечаются буквой Z. Зомби не занимает процессорного времени, но строка в таблице процессов остается, и соответствующие структуры ядра не освобождаются. После завершения родительского процесса "осиротевший" зомби на короткое время также становится потомком init, после чего уже "окончательно умирает".

*Дополнительно:*

- [Процессы, задачи, потоки и нити](http://citforum.ru/operating_systems/articles/process.shtml)

## Асинхронность

- **Асинхронный** буквально означает “не синхронный”. Например, отправка email асинхронная, потому что отправитель не ожидает получить ответ сразу же. В программировании “асинхронным” называют код, в котором компоненты посылают друг другу сообщения, не ожидая немедленного ответа.
- **Неблокирующий** - термин, чаще всего касающийся ввода-вывода. Он означает, что при вызове “неблокирующего” системного API управление сразу же будет возвращено программе, и она продолжит использовать свой квант процессорного времени. Обычные, простые в использовании системные вызовы блокирующие: они усыпляют вызывающий поток до тех пор, пока данные для ответа не будут готовы.
- **Событийный** означает, что компонент программы обрабатывает очередь событий с помощью цикла, а тем временем кто-либо добавляет события в очередь, формируя входные данные компонента, и забирает у него выходные данные.

Синхронные операции — операции, при которых мы получаем результат в результате блокирования [потока](https://habrahabr.ru/post/318374/#potoki) выполнения. Для простых вычислительных операций (сложение/умножение чисел) — это единственный вариант их совершения, для операций ввода/вывода — один из, при этом мы говорим, к примеру, *«попытайся прочитать из файла что-нибудь за 100мс»*, и если для чтения ничего нет — поток выполнения будет заблокирован на эти 100мс.

В некоторых случаях это допустимо (например, если мы делаем простое консольное приложение, либо какую-либо утилиту, цель которой — отработать и всё), но в некоторых — нет. К примеру, если мы так застрянем в потоке, в котором обрабатывается UI — наше приложение зависнет. За примерами далеко ходить не нужно — если javascript на сайте сделает `while(true);`, то перестанут вызываться какие-либо другие обработчики событий страницы и её придётся закрыть. Те же дела, если начать что-нибудь вычислять под Android'ом в обработчиках UI-событий (код которых вызывается в UI-потоке), это приведёт к появлению окна «приложение не отвечает, закрыть?» (подобные окна вызываются по [watchdog-таймеру](https://en.wikipedia.org/wiki/Watchdog_timer), который сбрасывается, когда выполнение возвращается обратно к системе UI).

Асинхронные операции — операции, при которых мы **просим совершить** некоторую операцию и можем каким-либо образом отслеживать процесс/результат её выполнения. Когда она будет выполнена — неизвестно, но мы можем продолжить заниматься другими делами.

Блокировки - это проблема для всех программ, требующих конкурентного выполнения, поскольку заблокированные потоки процесса засыпают и не получают процессорное время. Существует два различных, но взаимодополняющих способа устранить блокировки:

- неблокирующий режим ввода-вывода
- мультиплексирование с помощью системного API, такого как `select` либо `epoll`

*Дополнительно:*

- [Асинхронный ввод-вывод средствами POSIX](https://ps-group.github.io/os/nonblocking_io_posix)

### Event loop

Бесконечный цикл, который берёт **события** из очереди и как-то их обрабатывает. А в некоторых промежутках — смотрит, не произошло ли каких-нибудь **IO-событий**, либо не просрочились ли какие-либо **таймеры** — тогда добавляет в очередь событие об этом, чтобы потом обработать.

## Race condition

**Состояние гонки (race condition)**, также конкуренция — ошибка проектирования многопоточной системы или приложения, при которой работа системы или приложения зависит от того, в каком порядке выполняются части кода. Своё название ошибка получила от похожей ошибки проектирования электронных схем (см. Гонки сигналов).

Термин состояние гонки относится к инженерному жаргону и появился вследствие неаккуратного дословного перевода английского эквивалента. В более строгой академической среде принято использовать термин неопределённость параллелизма.

Состояние гонки — «плавающая» ошибка (гейзенбаг), проявляющаяся в случайные моменты времени и «пропадающая» при попытке её локализовать.

## Вытесняющая и кооперативная многозадачность

### Совместная или кооперативная многозадачность

Тип многозадачности, при котором следующая задача выполняется только после того, как текущая задача явно объявит себя готовой отдать процессорное время другим задачам. Как частный случай такое объявление подразумевается при попытке захвата уже занятого объекта [мьютекс](https://ru.wikipedia.org/wiki/Мьютекс) (ядро Linux), а также при ожидании поступления следующего сообщения от подсистемы пользовательского интерфейса (Windows версий до [3.x](https://ru.wikipedia.org/wiki/Windows_3.x) включительно, а также 16-битные приложения в [Windows 9x](https://ru.wikipedia.org/wiki/Windows_9x)).

Кооперативную многозадачность можно назвать многозадачностью «второй ступени», поскольку она использует более передовые методы, чем простое переключение задач, реализованное многими известными программами (например, [DOS Shell](https://ru.wikipedia.org/wiki/DOS_Shell) из [MS-DOS](https://ru.wikipedia.org/wiki/MS-DOS) 5.0). При простом переключении активная программа получает все процессорное время, а фоновые приложения полностью замораживаются. При кооперативной многозадачности приложение может захватить фактически столько процессорного времени, сколько оно считает нужным. Все приложения делят процессорное время, периодически передавая управление следующей задаче.

Преимущества кооперативной многозадачности: отсутствие необходимости защищать все разделяемые структуры данных объектами типа критических секций и мьютексов, что упрощает программирование, особенно перенос кода из однозадачных сред в многозадачные.

Недостатки: неспособность всех приложений работать в случае ошибки в одном из них, приводящей к отсутствию вызова операции «отдать процессорное время». Крайне затрудненная возможность реализации многозадачной архитектуры ввода-вывода в ядре ОС, позволяющей процессору исполнять одну задачу, в то время как другая задача инициировала операцию ввода-вывода и ждет её завершения.

### Вытесняющая, или приоритетная, многозадачность ([режим реального времени](https://ru.wikipedia.org/wiki/Режим_реального_времени))

Вид многозадачности, в котором [операционная система](https://ru.wikipedia.org/wiki/Операционная_система) сама передает управление от одной выполняемой программы другой в случае завершения операций ввода-вывода, возникновения событий в аппаратуре компьютера, истечения таймеров и квантов времени, или же поступлений тех или иных сигналов от одной программы к другой. В этом виде многозадачности процессор может быть переключен с исполнения одной программы на исполнение другой без всякого пожелания первой программы и буквально между любыми двумя инструкциями в её коде. Распределение процессорного времени осуществляется планировщиком процессов. К тому же каждой задаче может быть назначен пользователем или самой операционной системой определенный приоритет, что обеспечивает гибкое управление распределением процессорного времени между задачами (например, можно снизить приоритет ресурсоёмкой программе, снизив тем самым скорость её работы, но повысив производительность фоновых процессов). Этот вид многозадачности обеспечивает более быстрый отклик на действия пользователя.

Преимущества:

- возможность полной реализации многозадачного ввода-вывода в ядре ОС, когда ожидание завершения ввода-вывода одной программой позволяет процессору тем временем исполнять другую программу;
- cильное повышение надежности системы в целом, в сочетании с использованием защиты памяти — идеал в виде «ни одна программа пользовательского режима не может нарушить работу ОС в целом» становится достижимым хотя бы теоретически, вне вытесняющей многозадачности он не достижим даже в теории.
- возможность полного использования многопроцессорных и многоядерных систем.

Недостатки:

- необходимость особой дисциплины при написании кода, особые требования к его [реентерабельности](https://ru.wikipedia.org/wiki/Реентерабельность), к защите всех разделяемых и глобальных данных объектами типа критических секций и мьютексов.

## Системный вызов

**Системный вызов(system call)** — обращение прикладной программы к ядру операционной системы для выполнения какой-либо операции.

Современные операционные системы (ОС) предусматривают разделение времени между выполняющимися вычислительными процессами(многозадачность) и разделение полномочий, препятствующее обращению исполняемых программ к данным других программ и оборудованию. Ядро ОС исполняется в привилегированном режиме работы процессора. Для выполнения межпроцессорной операции или операции, требующей доступа к оборудованию, программа обращается к ядру, которое, в зависимости от полномочий вызывающего процесса, исполняет либо отказывает в исполнении такого вызова.

С точки зрения программиста, системный вызов обычно выглядит как вызов подпрограммы или функции из системной библиотеки. Однако системный вызов, как частный случай вызова такой функции или подпрограммы, следует отличать от более общего обращения к системной библиотеке, поскольку последнее может и не требовать выполнения привилегированных операций.

### epoll

 `epol`  - механизм асинхронного мультиплексирования ввода-вывода в Linux, предназначенный для эффективного управления множеством файловых дескрипторов. Он используется в высоконагруженных сетевых серверах, обработчиках событий и любых сценариях, где требуется следить за изменением состояния множества файловых дескрипторов (сокетов, FIFO, файлов и т. д.).

Раньше в UNIX-подобных системах использовались механизмы `select` и `poll`, но они имели **ключевые проблемы**:

1. **Линейная сложность O(n)** — каждый вызов требовал проверки всех дескрипторов.
2. **Ограничение по количеству файловых дескрипторов** (`FD_SETSIZE` в `select` ограничен 1024 в большинстве систем).
3. **Неэффективность при большом количестве соединений**, поскольку дескрипторы постоянно копируются между user space и kernel space.

`epoll` решает эти проблемы:  
✅ **Работает эффективно (O(1))**, даже при тысячах файловых дескрипторов.  
✅ **Событийный механизм** — уведомления приходят только при изменениях.  
✅ **Хранит дескрипторы в ядре** и не требует их пересылки при каждом вызове.



### NUMA Nodes (Non-Uniform Memory Access)

`NUMA` — это архитектура многопроцессорных систем, в которой память разделена на **узлы (nodes)**, каждый из которых локально связан с определённым набором процессоров.

📌 **Ключевая особенность:**

- Доступ к **локальной** памяти (той, которая привязана к процессору) — быстрый.
- Доступ к **удалённой** памяти (другого узла) — медленный, потому что требует взаимодействия через межпроцессорную шину.

Обычно в **многосокетных системах** (например, серверы с двумя и более CPU) каждый **сокет** формирует **свой NUMA-узел**, содержащий:  
✔ **CPU-ядра**  
✔ **Локальную память (RAM)**  
✔ **Связь с другими узлами** через межпроцессорные соединения (QPI, Infinity Fabric и др.)

Пример системы с двумя NUMA-узл**ами:**

```scss

+---------------------------+       +---------------------------+
| NUMA Node 0               |       | NUMA Node 1               |
|  - CPU 0, CPU 1, CPU 2... |       |  - CPU 8, CPU 9, CPU 10...|
|  - RAM (локальная)        |       |  - RAM (локальная)        |
+---------------------------+       +---------------------------+
           |                                   |
           Межпроцессорное соединение (медленное)
```

Зачем нужна NUMA?

🔹 Уменьшает задержки доступа к памяти.  
🔹 Позволяет лучше использовать кеш процессора.  
🔹 Оптимизирует работу с многопоточной нагрузкой.



### CPU Affinity (Привязка процессов к ядрам CPU)

**CPU affinity** — это механизм, который позволяет **закрепить процесс или поток за определёнными ядрами**.

📌 **Зачем это нужно?**

- **Снижает "переключение процессоров" (CPU migration).** Если процесс постоянно перескакивает между ядрами, он теряет кешированные данные, что снижает производительность.
- **Позволяет управлять нагрузкой** в NUMA-системах, привязывая процесс к локальному NUMA-узлу, чтобы уменьшить доступ к удалённой памяти.

На практике CPU affinity **реализуется** через affinity mask, просто в удобной форме. **Affinity mask** — битовая маска, определяющая, какие CPU разрешено использовать процессу или потоку. Пример:

```
CPU:   0   1   2   3  
MASK:  1   1   0   0 --> процесс работает только на CPU 0 и 1
```

## Разное

**Кеши процессора** — многоуровневая (L1, L2, L3) сверхбыстрая память в процессоре, хранящая часто используемые данные для уменьшения задержек при доступе к оперативной памяти.

**OOM Killer** — механизм в ядре Linux, завершает процессы при нехватке оперативной памяти, выбирая наименее критичные по заданным критериям.

**Сокет-бэклоги** — очередь входящих соединений в сокете, ожидающих принятия (`listen` + `accept` в TCP).

**Cache lines** — минимальная единица данных в кэше процессора, обычно 64 байта, загружаемая в кэш целиком при обращении к памяти.

**User space** — пространство выполнения пользовательских процессов, ограниченное в доступе к аппаратным ресурсам и системным функциям.

**Kernel space и user space** — разделение памяти ОС: kernel space выполняет привилегированные операции, user space — пользовательские программы.

**Hyper-Threading** — технология Intel, позволяющая одному физическому ядру выполнять два потока одновременно, увеличивая загрузку ALU и FPU.

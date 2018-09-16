## Магические методы PHP

- [**__construct()**](http://php.net/manual/ru/language.oop5.decon.php#object.construct) — метод конструктор, если он объявлен в классе то он будет вызываться при каждом создании объекта. Может принимать одно или несколько свойств.
- [**__destruct()**](http://php.net/manual/ru/language.oop5.decon.php#object.destruct) — деструктор вызывается при освобождении всех ссылок на объект в котором он находится или по завершению работы скрипта. Не может принимать свойства.
- [**__get()**](http://php.net/manual/ru/language.oop5.overloading.php#object.get) — будет выполнен при чтении данных из недоступных свойств.
- [**__set()**](http://php.net/manual/ru/language.oop5.overloading.php#object.set) — будет выполнен при записи данных в недоступные свойства.
- [**__isset()**](http://php.net/manual/ru/language.oop5.overloading.php#object.isset) будет выполнен при использовании isset() или empty() на недоступных свойствах.
- [**__unset()**](http://php.net/manual/ru/language.oop5.overloading.php#object.unset) — будет выполнен при вызове unset() на недоступном свойстве.
- [**__call()**](http://php.net/manual/ru/language.oop5.overloading.php#object.call) — данный метод вызывается при вызове недоступных методов класса.
- [**__callStatic()**](http://php.net/manual/ru/language.oop5.overloading.php#object.callstatic) —данный метод вызывается при вызове недоступных методов класса в статическом контексте.
- [**__toString()**](http://php.net/manual/ru/language.oop5.magic.php#object.tostring) — позволяет написать реакцию класса при возведении его в строку.
- [**__invoke()**](http://php.net/manual/ru/language.oop5.magic.php#object.invoke) — вызывается когда скрипт выполняет объект как функцию.
- [**__clone()**](http://php.net/manual/ru/language.oop5.cloning.php#object.clone) — клонирует объект.
- [**__sleep()**](http://php.net/manual/ru/language.oop5.magic.php#object.sleep) — метод вызывается перед выполнением функции serialize().
- [**__wakeup()**](http://php.net/manual/ru/language.oop5.magic.php#object.wakeup) — метод вызывается перед выполнением функции unserialize()
- [**__set_state()**](http://php.net/manual/ru/language.oop5.magic.php#object.set-state) — этот статический метод вызывается для тех классов, которые экспортируются функцией var_export()
- [**__debugInfo()**](http://php.net/manual/ru/language.oop5.magic.php#object.debuginfo) — метод вызывается функцией [var_dump()](http://php.net/manual/ru/function.var-dump.php), когда необходимо вывести список свойств объекта. Если этот метод не определен, тогда будут выведены все свойства объекта c модификаторами public, protected и private.




## Замыкания и лямда функции

Анонимные функции, также известные как замыкания (*closures*), позволяют создавать функции, не имеющие определенных имен. Они наиболее полезны в качестве значений [callback](http://php.net/manual/ru/language.pseudo-types.php#language.types.callback)-параметров, но также могут иметь и множество других применений.

Анонимные функции реализуются с использованием класса [Closure](http://php.net/manual/ru/class.closure.php).

В пхп замыканием считает любая анонимная функция, которая может использовать переменные из внешнего(для функции) контекста с помощью конструкции use.

```php
$message = 'привет';


$example = function () use ($message) {
    var_dump($message);
};
$example();
```





## SPL

Стандартная библиотека PHP (SPL) - это набор интерфейсов и классов, предназначенных для решения стандартных задач.

Не требуется никаких внешних библиотек для сборки этого расширения, и оно доступно по умолчанию в PHP 5.0.0 и выше.

SPL предоставляет ряд стандартных структур данных, итераторов для оббегания объектов, интерфейсов, стандартных исключений, некоторое количество классов для работы с файлами и предоставляет ряд функций, например [spl_autoload_register()](http://php.net/manual/ru/function.spl-autoload-register.php). 

Еще примеры:

- [Countable](http://php.net/manual/ru/class.countable.php)
- [OuterIterator](http://php.net/manual/ru/class.outeriterator.php)
- [RecursiveIterator](http://php.net/manual/ru/class.recursiveiterator.php)
- [SeekableIterator](http://php.net/manual/ru/class.seekableiterator.php)
- [ArrayObject](http://php.net/manual/ru/class.arrayobject.php)
- [SplObserver](http://php.net/manual/ru/class.splobserver.php)
- [SplSubject](http://php.net/manual/ru/class.splsubject.php)

## Self vs  Static

Позднее статическое связывание сохраняет имя класса указанного в последнем "неперенаправленном вызове". В случае статических вызовов это явно указанный класс (обычно слева от оператора [*::*](http://php.net/manual/ru/language.oop5.paamayim-nekudotayim.php)); в случае не статических вызовов это класс объекта. "Перенаправленный вызов" - это статический вызов, начинающийся с *self::*, *parent::*, *static::*, или, если двигаться вверх по иерархии классов,[forward_static_call()](http://php.net/manual/ru/function.forward-static-call.php). Функция [get_called_class()](http://php.net/manual/ru/function.get-called-class.php) может быть использована для получения строки с именем вызванного класса, а *static::* представляет ее область действия.

Само название "позднее статическое связывание" отражает в себе внутреннюю реализацию этой особенности. "Позднее связывание" отражает тот факт, что обращения через *static::* не будут вычисляться по отношению к классу, в котором вызываемый метод определен, а будут вычисляться на основе информации в ходе исполнения. Также эта особенность была названа "статическое связывание" потому, что она может быть использована (но не обязательно) в статических методах.

Говоря проще self - обращается непосредственно к классу, в котором описана ф-ция с его исопльзованием, а static - к классу к которому идет обращение из кода.

## Сессии

Как хранятся, как переопределить место хранения,  в какой момент и соклько раз обращаемся

## Как выполнить код после exit?

You can use [register_shutdown_function()](http://www.php.net/register_shutdown_function) to set a callback function which will be executed when PHP exits.

## Отладка и профилировка

## Анализаторы кода

## Сегфолт в пхп фпм, как поймать, как отловить трейс

## Где посмотреть список модулей пхп

## В каких случаях, не выполнится деструктор
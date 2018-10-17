# Разное о PHP

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

## PSR-0 vs PSR-4

Here are the major differences,

**1.** For example if you define that the `Acme\Foo\` namespace is anchored in `src/`,

- with PSR-0 it means it will look for `Acme\Foo\Bar` in `src/Acme/Foo/Bar.php`
- while in PSR-4 it will look for `Acme\Foo\Bar` in `src/Bar.php(where Bar class is)`.

**2.** PSR-4 does not convert underscores to directory separators

**3.** You would prefer using PSR-4 with namespaces

**4.** PSR-0 will not work even if the class name is different from file name, like considering above example:

- `Acme\Foo\Bar` ---> `src/Acme/Foo/Bar.php` (for Bar class) will work
- `Acme\Foo\Bar` ---> `src/Acme/Foo/Bar2.php` (for Bar class) will not work


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

## Composer

### composer.json vs composer.lock

`composer.json` - содержит допустимые "диапазоны" версий для пакетов

`composer.lock` - точные версии установленных пакетов, с которыми работает данная версия приложения

### install vs update vs require

`composer install` делает следующее:

- Проверяет существует ли `composer.lock`
- если нет, резолвит зависимости и создаёт его
- если `composer.lock` существует, устанавливает версии, указанные в нём

`composer update`:

- Проверяет `composer.json`
- Определяет последние версии на основе указанных в этом файле
- Устанавливает последние версии
- Обновляет `composer.lock` в соответствии с установленными

`composer require somePackage`:

For example if we want to add a dependency with the command line we will simply execute 

- composer.json file will be modified automatically and the new dependency will be added
- the dependency will be downloaded to the project

- http://php.net/manual/ru/class.splsubject.php)

## Self vs  Static

Позднее статическое связывание сохраняет имя класса указанного в последнем "неперенаправленном вызове". В случае статических вызовов это явно указанный класс (обычно слева от оператора [*::*](http://php.net/manual/ru/language.oop5.paamayim-nekudotayim.php)); в случае не статических вызовов это класс объекта. "Перенаправленный вызов" - это статический вызов, начинающийся с *self::*, *parent::*, *static::*, или, если двигаться вверх по иерархии классов,[forward_static_call()](http://php.net/manual/ru/function.forward-static-call.php). Функция [get_called_class()](http://php.net/manual/ru/function.get-called-class.php) может быть использована для получения строки с именем вызванного класса, а *static::* представляет ее область действия.

Само название "позднее статическое связывание" отражает в себе внутреннюю реализацию этой особенности. "Позднее связывание" отражает тот факт, что обращения через *static::* не будут вычисляться по отношению к классу, в котором вызываемый метод определен, а будут вычисляться на основе информации в ходе исполнения. Также эта особенность была названа "статическое связывание" потому, что она может быть использована (но не обязательно) в статических методах.

Говоря проще self - обращается непосредственно к классу, в котором описана ф-ция с его исопльзованием, а static - к классу к которому идет обращение из кода.

## Можем ли мы  гарантировать выполнения деструктора?

Деструктор не выполнится если:

1. Exit вызван в другом деструкторе.
2. Если другой деструктор бросает исключение.
3. Если мы пытаемся обработать исключение в деструкторе.
4. При получении SIGTERM в случае запуска через CLI.
5. Fatal Error

## Сессии

По умолчанию хранятся в файлах. Но можно переопределить:

```ini
session.save_handler = memcached 
session.save_path = «tcp://192.1680.10:11211, tcp://192.168.0.20:11211»
```

Основная функция **session_start()**  - создает сессию, либо возобновляет существующую, основываясь на идентификаторе сессии, переданном через GET- или POST-запрос, либо переданный через cookie.

Когда вызвана функция **session_start()** или когда сессия создается автоматически, PHP вызовет открытие и чтение обработчиков записи сессии. Это могут быть как встроенные обработчики, так и предоставляемые расширениями (например, SQLite или Memcached); или вообще определенный пользователем обработчик, заданный функцией [session_set_save_handler()](http://php.net/manual/ru/function.session-set-save-handler.php). Callback-функция чтения извлечет все существующие данные сессии (сохраненные в специальном сериализованном виде), десериализует их и занесет в суперглобальный массив $_SESSION, после чего вернет сохраненные данные обработчику сессий PH

## Как выполнить код после exit?

You can use [register_shutdown_function()](http://www.php.net/register_shutdown_function) to set a callback function which will be executed when PHP exits.

## Отладка и профилировка

## Анализаторы кода

- [php-sat](http://www.program-transformation.org/PHP/PhpSat) - Requires <http://strategoxt.org/>
- [PHP_Depend](http://pdepend.org/)
- [PHP_CodeSniffer](http://pear.php.net/package/PHP_CodeSniffer)
- [PHP Mess Detector](http://phpmd.org/)
- [PHPStan](https://github.com/phpstan/phpstan)
- [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer)
- [phan](https://github.com/phan/phan)

## Сегфолт в пхп фпм, как поймать, как отловить трейс

**Ошибка сегментации** ([англ.](https://ru.wikipedia.org/wiki/%D0%90%D0%BD%D0%B3%D0%BB%D0%B8%D0%B9%D1%81%D0%BA%D0%B8%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA) Segmentation fault, сокр. segfault, жарг. *сегфолт*) — ошибка, возникающая при попытке обращения к недоступным для записи участкам [памяти](https://ru.wikipedia.org/wiki/%D0%92%D0%B8%D1%80%D1%82%D1%83%D0%B0%D0%BB%D1%8C%D0%BD%D0%B0%D1%8F_%D0%BF%D0%B0%D0%BC%D1%8F%D1%82%D1%8C) либо при попытке изменить память запрещённым способом

## Cписок модулей пхп

`php -m`

## SPL

Стандартная библиотека PHP (SPL) - это набор интерфейсов и классов, предназначенных для решения стандартных задач. Не требуется никаких внешних библиотек для сборки этого расширения, и оно доступно по умолчанию в PHP 5.0.0 и выше.

SPL предоставляет ряд стандартных структур данных, итераторов для оббегания объектов, интерфейсов, стандартных исключений, некоторое количество классов для работы с файлами и предоставляет ряд функций, например [spl_autoload_register()](http://php.net/manual/ru/function.spl-autoload-register.php). 

Еще примеры:

- [Countable](http://php.net/manual/ru/class.countable.php)

- [OuterIterator](http://php.net/manual/ru/class.outeriterator.php)

- [RecursiveIterator](http://php.net/manual/ru/class.recursiveiterator.php)

- [SeekableIterator](http://php.net/manual/ru/class.seekableiterator.php)

- [ArrayObject](http://php.net/manual/ru/class.arrayobject.php)

- [SplObserver](http://php.net/manual/ru/class.splobserver.php)



## declare(strict_types=1);

By default, PHP will cast values of the wrong type into the expected scalar type if possible. For example, a function that is given an integer for a parameter that expects a string will get a variable of type string.

В случае включенного строгого режима будут фаталы при несовпадении типов. Строгий режим действует ТОЛЬКО внутри того файла, где он был объявлен.
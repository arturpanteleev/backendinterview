# PHP

## Нововведения разных версий PHP 

### 5.3

### 5.4

### 5.5

### 5.6

### 7.0

1. возможность обработки исключения, вместо фатальной ошибки
2. новые операторы сравнения ( <=>, ?? и другие)
3. анонимные классы
4. указание типа возвращаемого значения (return type declaration)
5. группировка для оператора use
6. работа с замыканиями (closure)
7. скалярные типы аргументов функции (scalar type hints)
8. опционально доступный "строгий режим" работы с типами (stitict mode)
9. изменена трактовка переменных и выражений
10. улучшена работа с генераторами ( ключевое слово yield from и другие изменения )
11. конструктор класса в стиле php4 (когда имя класса совпадает с именем метод) теперь генерирует E_DEPRECATED и будет удален в php8
12. изменено поведение побитовых операторов <<, >>  и других
13. изменено поведение функций funct_get_args и funct_get_arg
14. функция языка unserialize принимает дополнительный аргумент
15. функция языка list изменила поведение
16. изменено поведение цикла foreach (например в работе с внутренним итератором)
17. новый синтаксис unicode последовательностей

### 7.1

1. возвращаемый тип void
2. псевдотип iterable
3.  null в типизированных и возвращаемых параметрах
4. возможность использовать отрицательное значение для смещения в строках
5. разрешено использовать строковые ключи в конструкци
6. конвертация callable выражений в замыкание
7. Поддержка модификаторов видимости для констант класса
8. Ловить исключения можно объединяя несколько типов исключений в один блок 

### 7.2

1. Добавлена возможность загружать расширения по имени
2. Добавлена возможность перегружать абстрактные функции(Liskov)
3. Запрещено number_format() возвращать -0
4. Добавлена возможность конвертировать нумерованные ключи при приведении типов object/array
5. Запрещено передавать null в качестве параметра для get_class()
6. Вызов Count с параметром, который нельзя посчитать
7. Возможность расширения типа параметра
8. Добавлена возможность указывать запятую в конце группированных неймспейсов
9. Реализовано семейство функций socket_getaddrinfo
10. Улучшены TLS-константы
11. Object typehint
12. LDAP EXOP
13. В ядро PHP добавлена Libsodium
14. Добавлен алгоритм Argon2 в хешировании пароля
15. HashContext as Object
16. Добавлен отладчик PDO Prepared statements
17. Добавлен отладчик PDO Prepared statements v2
18. Расширенные типы строк для PDO
19. Добавлены опции JSON_INVALID_UTF8_IGNORE и JSON_INVALID_UTF8_SUBSTITUTE

## Магические методы PHP

[__construct()](http://php.net/manual/ru/language.oop5.decon.php#object.construct), [__destruct()](http://php.net/manual/ru/language.oop5.decon.php#object.destruct), [__call()](http://php.net/manual/ru/language.oop5.overloading.php#object.call), [__callStatic()](http://php.net/manual/ru/language.oop5.overloading.php#object.callstatic), [__get()](http://php.net/manual/ru/language.oop5.overloading.php#object.get), [__set()](http://php.net/manual/ru/language.oop5.overloading.php#object.set), [__isset()](http://php.net/manual/ru/language.oop5.overloading.php#object.isset), [__unset()](http://php.net/manual/ru/language.oop5.overloading.php#object.unset), [__sleep()](http://php.net/manual/ru/language.oop5.magic.php#object.sleep),[__wakeup()](http://php.net/manual/ru/language.oop5.magic.php#object.wakeup), [__toString()](http://php.net/manual/ru/language.oop5.magic.php#object.tostring), [__invoke()](http://php.net/manual/ru/language.oop5.magic.php#object.invoke), [__set_state()](http://php.net/manual/ru/language.oop5.magic.php#object.set-state), [__clone()](http://php.net/manual/ru/language.oop5.cloning.php#object.clone) и [__debugInfo()](http://php.net/manual/ru/language.oop5.magic.php#object.debuginfo) 

- **__construct()** — метод конструктор, если он объявлен в классе то он будет вызываться при каждом создании объекта. Может принимать одно или несколько свойств.
- **__destruct()** — деструктор вызывается при освобождении всех ссылок на объект в котором он находится или по завершению работы скрипта. Не может принимать свойства.
- **__get()** — будет выполнен при чтении данных из недоступных свойств.
- **__set()** — будет выполнен при записи данных в недоступные свойства.
- **__isset()** будет выполнен при использовании isset() или empty() на недоступных свойствах.
- **__unset()** — будет выполнен при вызове unset() на недоступном свойстве.
- **__call()** —данный метод вызывается при вызове недоступных методов класса.
- **__callStatic()** —данный метод вызывается при вызове недоступных методов класса в статическом контексте.
- **__toString()** — позволяет написать реакцию класса при возведении его в строку.
- **__invoke()** — вызывается когда скрипт выполняет объект как функцию.
- **__clone()** — клонирует объект.
- **__sleep()** — метод вызывается перед выполнением функции serialize().
- **__wakeup()** — метод вызывается перед выполнением функции unserialize()
- **__set_state()** — этот статический метод вызывается для тех классов, которые экспортируются функцией var_export()
- **__debugInfo()** — метод вызывается функцией [var_dump()](http://php.net/manual/ru/function.var-dump.php), когда необходимо вывести список свойств объекта. Если этот метод не определен, тогда будут выведены все свойства объекта c модификаторами public, protected и private.
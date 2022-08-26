## Основные нововведения php 

### [7.0](http://www.php.net/ChangeLog-7.php#7.0.0)

1. возможность обработки исключения, вместо фатальной ошибки
2. новые операторы сравнения ( <=>, ?? и другие)
3. анонимные классы
4. указание типа возвращаемого значения (return type declaration)
5. группировка для оператора use
6. работа с замыканиями (closure)
7. скалярные типы аргументов функции (scalar type hints)
8. опционально доступный "строгий режим" работы с типами (strict mode)
9. изменена трактовка переменных и выражений
10. улучшена работа с генераторами (ключевое слово yield from и другие изменения)
11. конструктор класса в стиле php4 (когда имя класса совпадает с именем метод) теперь генерирует E_DEPRECATED и будет удален в php8
12. изменено поведение побитовых операторов <<, >>  и других
13. изменено поведение функций func_get_args и func_get_arg
14. функция языка unserialize принимает дополнительный аргумент
15. функция языка list изменила поведение
16. изменено поведение цикла foreach (например в работе с внутренним итератором)
17. новый синтаксис unicode последовательностей

## [7.1](http://php.net/ChangeLog-7.php#7.1.0)

1. возвращаемый тип void
2. псевдотип iterable
3. null в типизированных и возвращаемых параметрах
4. возможность использовать отрицательное значение для смещения в строках
5. разрешено использовать строковые ключи в конструкци
6. конвертация callable выражений в замыкание
7. Поддержка модификаторов видимости для констант класса
8. Ловить исключения можно объединяя несколько типов исключений в один блок 

## [7.2](http://php.net/ChangeLog-7.php#7.2.0)

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

### [7.3](http://www.php.net/ChangeLog-7.php#7.3.0)

1. Смягчение требований к синтаксису Heredoc и Nowdoc
2. Поддержка конечных запятых в вызовах функций и методов
3. Ссылки в `list()`
4. Функция `image2wbmp()` объявлена устаревшей
5. Флаги `FILTER_FLAG_SCHEME_REQUIRED` и `FILTER_FLAG_HOST_REQUIRED` при использовании `FILTER_VALIDATE_URL` объявлены устаревшими
6. Регистро-независимые константы объявлены устаревшими
7. Опциональный выброс исключений при ошибках в функциях `json_encode` и `json_decode`
8. Добавление функции `is_countable()`
9. Добавление функций `array_key_first()` и `array_key_last()`

### 7.4

- Типизированные свойства классов
- Предзагрузка для улучшения производительности
- Стрелочные функции для короткой записи анонимных функций
- Присваивающий оператор объединения с null (??=)
- Ковариантность/контравариантность в сигнатурах унаследованных методов
- Интерфейс внешних функций, открывающий новые возможности для разработки расширений на PHP
- Оператор распаковки в массивах

### [8](https://www.php.net/releases/8.0)

1. Именованные аргументы
2. Атрибуты
3. Объявление свойств в конструкторе
4. Union types
5. Выражение Match
6. Оператор Nullsafe
7. Улучшенное сравнение строк и чисел
8. Ошибки согласованности типов для встроенных функций

### 8.1

- [Intersection Types](https://php.watch/versions/8.1/intersection-types)
- [Enums](https://php.watch/versions/8.1/enums)
- [`never` return type](https://php.watch/versions/8.1/never-return-type)
- [Readonly Properties](https://php.watch/versions/8.1/readonly)
- [Fibers](https://php.watch/versions/8.1/fibers)
- [`final` class constants](https://php.watch/versions/8.1/final-class-const)
- [New `fsync` and `fdatasync` functions](https://php.watch/versions/8.1/fsync-fdatasync)
- [New `array_is_list` function](https://php.watch/versions/8.1/array_is_list)
- [New Sodium `XChaCha20` functions](https://php.watch/versions/8.1/Sodium-XChaCha20-functions)
- [`$_FILES`: New `full_path` value for directory-uploads](https://php.watch/versions/8.1/$_FILES-full-path)
- [Intl: New `IntlDatePatternGenerator` class](https://php.watch/versions/8.1/IntlDatePatternGenerator)
- [First-class Callable Syntax](https://php.watch/versions/8.1/first-class-callable-syntax)
- [GD: AVIF image support](https://php.watch/versions/8.1/gd-avif)
- [Phar: Added OpenSSL-256 and OpenSSL-512 signature algorithms](https://php.watch/versions/8.1/phar-openssl-256-openssl-512)
- [GD: Lossless WebP encoding support](https://php.watch/versions/8.1/gd-webp-lossless)
- [New `#[ReturnTypeWillChange\]` attribute](https://php.watch/versions/8.1/ReturnTypeWillChange)
- [Array unpacking support for string-keyed arrays](https://php.watch/versions/8.1/spread-operator-string-array-keys)
- [Explicit Octal numeral notation](https://php.watch/versions/8.1/explicit-octal-notation)
- [Hash functions accept algorithm-specific `$options`](https://php.watch/versions/8.1/hash-options)
- [MurmurHash3 hash algorithm support](https://php.watch/versions/8.1/MurmurHash3)
- [xxHash hash algorithms support](https://php.watch/versions/8.1/xxHash)
- [FPM: Configurable child-process spawn rate](https://php.watch/versions/8.1/fpm-pm-max_spawn_rate)
- [Curl: DNS-over-HTTPS support](https://php.watch/versions/8.1/Curl-CURLOPT_DOH_URL)
- [Curl: File uploads from strings with `CURLStringFile`](https://php.watch/versions/8.1/CURLStringFile)
- [MySQLi: New `MYSQLI_REFRESH_REPLICA` constant](https://php.watch/versions/8.1/MYSQLI_REFRESH_REPLICA)
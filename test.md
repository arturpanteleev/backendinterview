# Тестирование

## Уровни Тестирования

### Модульное тестирование (Unit Testing)

Компонентное (модульное) тестирование проверяет функциональность и ищет дефекты в частях приложения, которые доступны и могут быть протестированы по-отдельности (модули программ, объекты, классы, функции и т.д.).

#### AAA(arrange-act-assert)

Шаблон для форматирования Unit тестов. Обозначающий разделения теста на 3 части

1. Arrange - все необходимые подготовки и входные данные
2. Act - собственно вызов того метода который вы тестируете
3. Assert - проверки, что метод делает то что надо

### Интеграционное тестирование (Integration Testing)

Проверяется взаимодействие между компонентами системы после проведения компонентного тестирования.

### Системное тестирование (System Testing)

Основной задачей системного тестирования является проверка как функциональных, так и не функциональных требований в системе в целом. При этом выявляются дефекты, такие как неверное использование ресурсов системы, непредусмотренные комбинации данных пользовательского уровня, несовместимость с окружением, непредусмотренные сценарии использования, отсутствующая или неверная функциональность, неудобство использования и т.д.

### Операционное тестирование (Release Testing).

Даже если система удовлетворяет всем требованиям, важно убедиться в том, что она удовлетворяет нуждам пользователя и выполняет свою роль в среде своей эксплуатации, как это было определено в бизнес моделе системы. Следует учесть, что и бизнес модель может содержать ошибки. Поэтому так важно провести операционное тестирование как финальный шаг валидации. Кроме этого, тестирование в среде эксплуатации позволяет выявить и нефункциональные проблемы, такие как: конфликт с другими системами, смежными в области бизнеса или в программных и электронных окружениях; недостаточная производительность системы в среде эксплуатации и др. Очевидно, что нахождение подобных вещей на стадии внедрения — критичная и дорогостоящая проблема. Поэтому так важно проведение не только верификации, но и валидации, с самых ранних этапов разработки ПО.

### Приемочное тестирование (Acceptance Testing)

Формальный процесс тестирования, который проверяет соответствие системы требованиям и проводится с целью:
•	определения удовлетворяет ли система приемочным критериям;
•	вынесения решения заказчиком или другим уполномоченным лицом принимается приложение или нет.

## Виды / типы тестирования

#### Функциональные виды тестирования

- Функциональное тестирование (Functional testing)

- Тестирование пользовательского интерфейса (GUI Testing)
- Тестирование безопасности (Security and Access Control Testing)
- Тестирование взаимодействия (Interoperability Testing)

#### Нефункциональные виды тестирования

- Все виды тестирования производительности:
- нагрузочное тестирование (Performance and Load Testing)
- стрессовое тестирование (Stress Testing)
- тестирование стабильности или надежности (Stability / Reliability Testing)
- объемное тестирование (Volume Testing)
- Тестирование установки (Installation testing)
- Тестирование удобства пользования (Usability Testing)
- Тестирование на отказ и восстановление (Failover and Recovery Testing)
- Конфигурационное тестирование (Configuration Testing)

#### Связанные с изменениями виды тестирования

- Дымовое тестирование (Smoke Testing)
- Регрессионное тестирование (Regression Testing)
- Повторное тестирование (Re-testing)
- Тестирование сборки (Build Verification Test)
- Санитарное тестирование или проверка согласованности/исправности (Sanity Testing)

## Фреймворки/инструменты для тестирования

### PHP Unit
PHP Unit - самый популярный фреймворк для модульного тестирования в PHP.

PHPUnit is based on the idea that developers should be able to find mistakes in their newly committed code quickly and assert that no [code regression](https://en.wikipedia.org/wiki/Regression_testing) has occurred in other parts of the code base. Much like other [unit testing](https://en.wikipedia.org/wiki/Unit_testing) frameworks, PHPUnit uses [assertions](https://en.wikipedia.org/wiki/XUnit#Assertions) to verify that the behavior of the specific component - or *"unit"* - being tested behaves as expected.

**Data Provider**

Метод, являющийся *data provider*-ом, должен возвращать массив массивов или объект, реализующий интерфейс `Iterator`. Метод, являющийся тестом, будет вызван несколько раз - с каждым массивом и в качестве аргументов будет передано содержимое массива.

Некоторые ключевые моменты при использовании *data provider*-а:

- Метод *data provider*-а должен быть публичным (`public`).
- Метод *data provider*-а должен возвращать массив собранных данных.
- Метод теста должен использовать аннотацию `@dataProvider` чтобы указать какой метод использовать в качестве *data provider*-а.

**Mock vs Stub**

The `createMock` method is used to create three mostly known test doubles. It's how you configure the object makes it a dummy, a stub, or a mock.

You can also create test stubs with the mock builder (`getMockBuilder` returns the mock builder). It's just another way of doing the same thing that lets you to tweak some additional mock options with a fluent interface (see [the documentation](https://phpunit.de/manual/current/en/test-doubles.html#test-doubles.mock-objects) for more).

Dummy is passed around, but never actually called, or if it's called it responds with a default answer (mostly `null`). It mainly exists to satisfy a list of arguments.

```php
$dummy = $this->createMock(SomeClass::class);

// SUT - System Under Test
$sut->action($dummy);
```

Stubs are used with query like methods - methods that return things, but it's not important if they're actually called.

```php
$stub = $this->createMock(SomeClass::class);
$stub->method('getSomething')
    ->willReturn('foo');

$sut->action($stub);
```

Mocks are used with command like methods - it's important that they're called, and we don't care much about their return value (command methods don't usually return any value).

```php
$mock = $this->createMock(SomeClass::class);
$mock->expects($this->once())
    ->method('doSomething')
    ->with('bar');

$sut->action($mock);
```

Expectations will be verified automatically after your test method finished executing. In the example above, the test will fail if the method `doSomething` wasn't called on `SomeClass`, or it was called with arguments different to the ones you configured.


### Codeception

Сodeception это, надстройка над PHPUnit(или любым другим тест фреймворком). Она позволяет выполнять сценарные тесты на движке PHPUnit. При этом все ваши существующие тесты для PHPUnit Codeception сможет подхватит без каких-либо проблем. К ним вы сможете легко добавить функциональные и приемочные тесты. Очень многие задачи, требующие костылей (например, интеграция с [Selenium](https://automated-testing.info/tags/webdriver), с БД) в Codeception уже решены.

###  Behat

Behat - это инструмент BDD.

BDD - behaviour-driven development - это разработка, основанная на описании поведения. То есть, есть специальный человек(или люди) который пишет описания вида "я как пользователь хочу **когда** нажали кнопку пуск **тогда** показывалось меню как на картинке". (там есть специально выделенные ключевые слова). 

В чем преимущество BDD?

- **тесты** читаемые для не программистов.
- их легко изменять. Они часто пишутся почти на чистом английском.
- их теперь может писать product owner или другие заинтересованные лица.
- результаты выполнения тестов более "человечные".
- **тесты** не зависят от целевого языка программирования. Миграция на другой язык сильно упрощается.

И именно для этих целей Behat и используется. Позволяет писать тесты человекопонятным английским языком в формате Given-When-Then, преобразуя эти инструкции в вызов автотестов.

### Selenium 

*Selenium -- это инструмент для автоматизированного управления браузерами.* Наиболее популярной областью применения Selenium является автоматизация тестирования веб-приложений. Однако при помощи Selenium можно  автоматизировать любые другие рутинные действия, выполняемые через браузер(клик на кнопку, наведение мыши на объект, печать в инпут и т.д).

## TDD

**Разработка через тестирование** (test-driven development, TDD) — техника [разработки программного обеспечения](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%BA%D0%B0_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE_%D0%BE%D0%B1%D0%B5%D1%81%D0%BF%D0%B5%D1%87%D0%B5%D0%BD%D0%B8%D1%8F), которая основывается на повторении очень коротких циклов разработки: сначала пишется тест, покрывающий желаемое изменение, затем пишется код, который позволит пройти тест, и под конец проводится рефакторинг нового кода к соответствующим стандартам.

*Дополнительно:*

* [Тестирование. Фундаментальная теория](https://dou.ua/forums/topic/13389/) - самый лучший
* [PHPUnit Getting Started](https://phpunit.de/getting-started/phpunit-7.html) 
* [Unit Testing Tutorial Part I: Introduction to PHPUnit](https://jtreminio.com/blog/unit-testing-tutorial-part-i-introduction-to-phpunit/)
* [ProTesting](http://www.protesting.ru)
* [PHPUnit для начинающих](https://phpprofi.ru/series/show/1)
* https://codeception.com/05-06-2013/specification-testing-coparison.html



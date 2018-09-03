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


# Побитовые операторы

Побитовые операторы интерпретируют операнды как последовательность из 32 битов (нулей и единиц). Они производят операции, используя двоичное представление числа, и возвращают новую последовательность из 32 бит (число) в качестве результата.

## Формат 32-битного целого числа со знаком
Что такое двоичная система счисления, вам, надеюсь, уже известно. При разборе побитовых операций мы будем обсуждать именно двоичное представление чисел, из 32 бит.

Старший бит слева – это научное название для самого обычного порядка записи цифр (от большего разряда к меньшему). При этом, если больший разряд отсутствует, то соответствующий бит равен нулю.

Примеры представления чисел в двоичной системе:
```
a = 0;  // 00000000000000000000000000000000
a = 1;  // 00000000000000000000000000000001
a = 2;  // 00000000000000000000000000000010
a = 3;  // 00000000000000000000000000000011
a = 255;// 00000000000000000000000011111111
```
Обратите внимание, каждое число состоит ровно из 32-битов.

Дополнение до двойки – это название способа поддержки отрицательных чисел.

Двоичный вид числа, обратного данному (например, 5 и -5) получается путём обращения всех битов с прибавлением 1.

То есть, нули заменяются на единицы, единицы – на нули и к числу прибавляется 1. Получается внутреннее представление того же числа, но со знаком минус.

Например, вот число 314:

```00000000000000000000000100111010```

Чтобы получить -314, первый шаг – обратить биты числа: заменить 0 на 1, а 1 на 0:

```11111111111111111111111011000101```

Второй шаг – к полученному двоичному числу прибавить единицу, обычным двоичным сложением: 11111111111111111111111011000101 + 1 = 11111111111111111111111011000110.

Итак, мы получили:

```-314 = 11111111111111111111111011000110```

Принцип дополнения до двойки делит все двоичные представления на два множества: если крайний-левый бит равен 0 – число положительное, если 1 – число отрицательное. Поэтому этот бит называется знаковым битом.


*Дополнительно:*
- [Основы JavaScript Побитовые операторы](https://learn.javascript.ru/bitwise-operators)
- [Побитовые операторы](http://php.net/manual/ru/language.operators.bitwise.php)

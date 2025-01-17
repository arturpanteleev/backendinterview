# Нормализация и нормальные формы

**Нормализация** — это процесс организации данных в базе данных, включающий создание таблиц и установление отношений между ними в соответствии с правилами (нормальными формами), которые обеспечивают защиту данных и делают базу данных более гибкой, устраняя избыточность и несогласованные зависимости.

Процесс проектирования БД с использованием метода НФ является итерационным и заключается в последовательном переводе отношения из 1НФ в НФ более высокого порядка по определенным правилам. Каждая следующая НФ ограничивается определенным типом функциональных зависимостей и устранением соответствующих аномалий при выполнении операций над отношениями БД, а также сохранении свойств предшествующих НФ.

**Нормальная форма** — требование, предъявляемое к структуре таблиц в теории реляционных баз данных для устранения из базы избыточных функциональных зависимостей между атрибутами (полями таблиц).

**Функциональная зависимость** между атрибутами (множествами атрибутов) X и Y означает, что для любого допустимого набора кортежей в данном отношении: если два кортежа совпадают по значению X, то они совпадают по значению Y. Например, если значение атрибута «Название компании» — Canonical Ltd, то значением атрибута «Штаб-квартира» в таком кортеже всегда будет Millbank Tower, London, United Kingdom. Обозначение: {X} -> {Y}.

**Метод нормальных форм (НФ)** состоит в сборе информации об объектах решения задачи в рамках одного отношения и последующей декомпозиции этого отношения на несколько взаимосвязанных отношений на основе процедур нормализации отношений. 

**Цель нормализации**: исключить избыточное дублирование данных, которое является причиной аномалий, возникших при добавлении, редактировании и удалении кортежей(строк таблицы).

**Аномалией** называется такая ситуация в таблице БД, которая приводит к противоречию в БД либо существенно усложняет обработку БД. Причиной является излишнее дублирование данных в таблице, которое вызывается наличием функциональных зависимостей от не ключевых атрибутов.

**Аномалии-модификации** проявляются в том, что изменение одних данных может повлечь просмотр всей таблицы и соответствующее изменение некоторых записей таблицы.

**Аномалии-удаления** — при удалении какого либо кортежа из таблицы может пропасть информация, которая не связана на прямую с удаляемой записью.

**Аномалии-добавления** возникают, когда информацию в таблицу нельзя поместить, пока она не полная, либо вставка записи требует дополнительного просмотра таблицы.  

#### Первая нормальная форма

Отношение находится в 1НФ, если все его атрибуты являются простыми, все используемые домены должны содержать только скалярные значения. Не должно быть повторений строк в таблице.

Например, есть таблица «Автомобили»:

| Фирма  | Модели      |
|--------|-------------|
| BMW    | M5, X5M, M1 |
| Nissan | GT-R        |

Нарушение нормализации 1НФ происходит в моделях BMW, т.к. в одной ячейке содержится список из 3 элементов: M5, X5M, M1, т.е. он не является атомарным. Преобразуем таблицу к 1НФ:

| Фирма  | Модели |
|--------|--------|
| BMW    | M5     |
| BMW    | X5M    |
| BMW    | M1     |
| Nissan | GT-R   |

#### Вторая нормальная форма

Отношение находится во 2НФ, если оно находится в 1НФ и каждый не ключевой атрибут неприводимо зависит от Первичного Ключа(ПК).

Неприводимость означает, что в составе потенциального ключа отсутствует меньшее подмножество атрибутов, от которого можно также вывести данную функциональную зависимость.

Например, дана таблица:

| Модель | Фирма  | Цена    | Скидка |
|--------|--------|---------|--------|
| M5     | BMW    | 5500000 | 5%     |
| X5M    | BMW    | 6000000 | 5%     |
| M1     | BMW    | 2500000 | 5%     |
| GT-R   | Nissan | 5000000 | 10%    |

Таблица находится в первой нормальной форме, но не во второй. Цена машины зависит от модели и фирмы. Скидка зависит от фирмы, то есть зависимость от первичного ключа неполная. Исправляется это путем декомпозиции на два отношения, в которых не ключевые атрибуты зависят от ПК.

| Модель | Фирма  | Цена    |
|--------|--------|---------|
| M5     | BMW    | 5500000 |
| X5M    | BMW    | 6000000 |
| M1     | BMW    | 2500000 |
| GT-R   | Nissan | 5000000 |

| Фирма  | Скидка |
|--------|--------|
| BMW    | 5%     |
| Nissan | 10%    |

#### Третья нормальная форма

Отношение находится в 3НФ, когда находится во 2НФ и каждый не ключевой атрибут нетранзитивно зависит от первичного ключа. Проще говоря, второе правило требует выносить все не ключевые поля, содержимое которых может относиться к нескольким записям таблицы в отдельные таблицы.

Рассмотрим таблицу:

| Модель | Магазин    | Телефон  |
|--------|------------|----------|
| BMW    | Риал-авто  | 87-33-98 |
| Audi   | Риал-авто  | 87-33-98 |
| Nissan | Некст-Авто | 94-54-12 |

Таблица находится во 2НФ, но не в 3НФ.

В отношении атрибут «Модель» является первичным ключом. Личных телефонов у автомобилей нет, и телефон зависит исключительно от магазина.

Таким образом, в отношении существуют следующие функциональные зависимости: Модель → Магазин, Магазин → Телефон, Модель → Телефон.

Зависимость Модель → Телефон является транзитивной, следовательно, отношение не находится в 3НФ.

В результате разделения исходного отношения получаются два отношения, находящиеся в 3НФ:

| Магазин    | Телефон  |
|------------|----------|
| Риал-авто  | 87-33-98 |
| Некст-Авто | 94-54-12 |

| Модель | Магазин    |
|--------|------------|
| BMW    | Риал-авто  |
| Audi   | Риал-авто  |
| Nissan | Некст-Авто |

#### Нормальная форма Бойса-Кодда (НФБК) (частная форма третьей нормальной формы)

Определение 3НФ не совсем подходит для следующих отношений:

1) отношение имеет две или более потенциальных ключа;

2) два и более потенциальных ключа являются составными;

3) они пересекаются, т.е. имеют хотя бы один атрибут.

Для отношений, имеющих один потенциальный ключ (первичный), НФБК является 3НФ.

Отношение находится в НФБК, когда каждая нетривиальная и неприводимая слева функциональная зависимость обладает потенциальным ключом в качестве детерминанта.

Предположим, рассматривается отношение, представляющее данные о бронировании стоянки на день:

| Номер стоянки | Время начала | Время окончания | Тариф      |
|---------------|--------------|-----------------|------------|
| 1             | 09:30        | 10:30           | Бережливый |
| 1             | 11:00        | 12:00           | Бережливый |
| 1             | 14:00        | 15:30           | Стандарт   |
| 2             | 10:00        | 12:00           | Премиум-В  |
| 2             | 12:00        | 14:00           | Премиум-В  |
| 2             | 15:00        | 18:00           | Премиум-А  |

Тариф имеет уникальное название и зависит от выбранной стоянки и наличии льгот, в частности:

- «Бережливый»: стоянка 1 для льготников
- «Стандарт»: стоянка 1 для не льготников
- «Премиум-А»: стоянка 2 для льготников
- «Премиум-B»: стоянка 2 для не льготников.

Таким образом, возможны следующие составные первичные ключи: {Номер стоянки, Время начала}, {Номер стоянки, Время окончания}, {Тариф, Время начала}, {Тариф, Время окончания}.

Отношение находится в 3НФ. Требования второй нормальной формы выполняются, так как все атрибуты входят в какой-то из потенциальных ключей, а неключевых атрибутов в отношении нет. Также нет и транзитивных зависимостей, что соответствует требованиям третьей нормальной формы. Тем не менее существует функциональная зависимость Тариф → Номер стоянки, в которой левая часть (детерминант) не является потенциальным ключом отношения, то есть отношение не находится в нормальной форме Бойса — Кодда.

Недостатком данной структуры является то, что, например, по ошибке можно приписать тариф «Бережливый» к бронированию второй стоянки, хотя он может относиться только к первой стоянки.

Можно улучшить структуру с помощью декомпозиции отношения на два и добавления атрибута 

**Имеет льготы**, получив отношения, удовлетворяющие НФБК (подчёркнуты атрибуты, входящие в первичный ключ.):

Тарифы

| Тариф      | Номер стоянки | Имеет льготы |
|------------|---------------|--------------|
| Бережливый | 1             | Да           |
| Стандарт   | 1             | Нет          |
| Премиум-А  | 2             | Да           |
| Премиум-В  | 2             | Нет          |

Бронирование

| Тариф      | Время начала | Время окончания |
|------------|--------------|-----------------|
| Бережливый | 09:30        | 10:30           |
| Бережливый | 11:00        | 12:00           |
| Стандарт   | 14:00        | 15:30           |
| Премиум-В  | 10:00        | 12:00           |
| Премиум-В  | 12:00        | 14:00           |
| Премиум-А  | 15:00        | 18:00           |

Также еще есть несколько нормальных форм(4,5,Доменно-ключевая,6) но на них всем похуй.

***Дополнительное чтение:***

[Нормализация отношений. Шесть нормальных форм](https://habr.com/post/254773/)

[Лекция № 10. Нормальные формы](http://www.libma.ru/kompyutery_i_internet/bazy_dannyh_konspekt_lekcii/p10.php)

[4.2.Теория нормальных форм.](http://www.mstu.edu.ru/study/materials/zelenkov/ch_4_2.html)
# Массив

Структура данных, хранящая набор значений (элементов массива), идентифицируемых по индексу или набору индексов, принимающих целые (или приводимые к целым) значения из некоторого заданного непрерывного диапазона. 

## Сортировки массивов

### Сортировка пузырьком / Bubble sort

Или **сортировка простыми обменами**. Обходим массив от начала до конца, попутно меняя местами неотсортированные соседние элементы. В результате первого прохода на последнее место «всплывёт» максимальный элемент. Теперь снова обходим неотсортированную часть массива (от первого элемента до предпоследнего) и меняем по пути неотсортированных соседей. Второй по величине элемент окажется на предпоследнем месте. Если за проход не произошло ни одного обмена, то массив отсортирован. Продолжая в том же духе, будем обходить всё уменьшающуюся неотсортированную часть массива, запихивая найденные максимумы в конец. Очевидно, не более чем после n итераций массив будет отсортирован. 

```php
<?php
    
/**
 * Worst O(n^2)
 * Average O(n^2)
 * Best O(n)
 */
function bubbleSort(array $array): array
{
    $length = count($array);
    for ($i = $length - 1; $i > 0; $i--) {
        $changes = false;
        for ($j = 0; $j < $i; $j++) {
            if ($array[$j] > $array[$j + 1]) {
                [$array[$j], $array[$j + 1]] = [$array[$j + 1], $array[$j]];
                $changes = true;
            }
        }
        if (!$changes) {
            return $array;
        }
    }

    return $array;
}

$array = [5, 25, 10, 7, 6, 20, 21, 1, 2, 13];
print_r(bubbleSort($array));
```

### Сортировка вставками / Insertion sort

Элементы входной последовательности просматриваются по одному, и каждый новый поступивший элемент размещается в подходящее место среди ранее упорядоченных элементов.

```php
<?php
/**
 * Worst O(n^2)
 * Average O(n^2)
 * Best O(n)
 */ 
function insertSort(array $arr): array 
{
    $count = count($arr);
  
    for ($i = 1; $i < $count; $i++) {
        for ($j = $i; $j >= 1 && $arr[$j] < $arr[$j-1]; $j--) {
           [$arr[$j], $arr[$j-1]] = [$arr[$j-1], $arr[$j]];
        }
    }
  
    return $arr;
}

$array = [3,4,1,2,5,9,6,7,8];
print_r(insertSort($array));

```

#### Сортировка выбором / Selection sort

На очередной итерации будем находить минимум в массиве после текущего элемента и менять его с ним, если надо. Таким образом, после i-ой итерации первые i элементов будут стоять на своих местах. Нужно отметить, что эту сортировку можно реализовать двумя способами – сохраняя минимум и его индекс или просто переставляя текущий элемент с рассматриваемым, если они стоят в неправильном порядке. 

```php
/**
 * Worst O(n^2)
 * Average O(n^2)
 * Best O(n^2)
 */
function selectionSort(array $arr): array 
{
    $count = count($arr);
    
    for ($i = 0; $i < $count - 1; $i++) {
        $min = $i;
        
        for ($j = $i + 1; $j < $count; $j++) {
            if ($arr[$j] < $arr[$min]) {
                $min = $j;
            }
        }
        
        if ($min != $i) {
            [$arr[$i], $arr[$min]] = [$arr[$min], $arr[$i]];
        }
    }
    
    return $arr;
}

$array = [3,4,1,2,5,9,6,7,8];
print_r(selectionSort($array));
```

#### Быстрая сортировка / Quicksort

Выберем некоторый опорный элемент(пивот). После этого перекинем все элементы, меньшие его, налево, а большие – направо. Рекурсивно вызовемся от каждой из частей. В итоге получим отсортированный массив, так как каждый элемент меньше опорного стоял раньше каждого большего опорного. Асимптотика: **O(n logn)** в среднем и лучшем случае, **O(n^2)**. Наихудшая оценка достигается при неудачном выборе опорного элемента. Идем одновременно слева и справа, находим пару элементов, таких, что левый элемент больше опорного, а правый меньше, и меняем их местами. 

```php
/**
 * Worst O(n^2)
 * Average O(n logn)
 * Best  O(n logn)
 */
function quickSort(array &$array, int $low, int $high): void 
{
    if ($low < $high) {
        $pivot = partition($array, $low, $high);

        quickSort($array, $low, $pivot - 1);
        quickSort($array, $pivot + 1, $high);
    }
}

function partition(array &$array, int $low, int $high): int
{
    $pivot = $array[$high];
    $wall = $low;

    for ($i = $low; $i < $high; $i++) {
        if ($array[$i] < $pivot) {
            [$array[$i], $array[$wall]] = [$array[$wall], $array[$i]];
            $wall++;
        }
    }

    [$array[$wall], $array[$i]] = [$array[$i], $array[$wall]];

    return $wall;
}

$array = [3, 4, 1, 2, 5, 9, 6, 7, 8];
quickSort($array, 0, count($array) - 1);
print_r($array);
```

 ```php
function simpleQuickSort(array $array): array
{
    if (count($array) <= 1) {
        return $array;
    } else {
        $pivot = $array[0];
        $left = [];
        $right = [];
        for ($i = 1; $i < count($array); $i++) {
            if ($array[$i] < $pivot) {
                $left[] = $array[$i];
            } else {
                $right[] = $array[$i];
            }
        }
        return array_merge(simpleQuickSort($left), [$pivot], simpleQuickSort($right));
    }
}

$array = [3, 4, 1, 2, 5, 9, 6, 7, 8];
print_r(simpleQuickSort($array));
 ```

#### Сортировка слиянием / Merge sort

Сортировка, основанная на парадигме «разделяй и властвуй». Разделим массив пополам, рекурсивно отсортируем части, после чего выполним процедуру слияния: поддерживаем два указателя, один на текущий элемент первой части, второй – на текущий элемент второй части. Из этих двух элементов выбираем минимальный, вставляем в ответ и сдвигаем указатель, соответствующий минимуму. Слияние работает за **O(n)**, уровней всего logn, поэтому асимптотика **O(nlogn)**. Эффективно заранее создать временный массив и передать его в качестве аргумента функции. Эта сортировка рекурсивна, как и быстрая, а потому возможен переход на квадратичную при небольшом числе элементов.

```php
<?php
 /**
 * Worst O(n log n)
 * Average O(n log n)
 * Best O(n log n)	
 */ 
function mergeSort(array $array): array
{
    [$half1, $half2] = array_chunk($array, count($array) / 2);

    if (count($half1) > 1) {
        $half1 = mergeSort($half1);
    }

    if (count($half2) > 1) {
        $half2 = mergeSort($half2);
    }

    return merge($half1, $half2);
}

function merge(array $arr1, array $arr2): array
{
    $resArray = [];
    $i = 0;
    $j = 0;

    while (true) {
        if (!isset($arr1[$i])) {
            $resArray = array_merge($resArray, array_slice($arr2, $j));
            break;
        }

        if (!isset($arr2[$j])) {
            $resArray = array_merge($resArray, array_slice($arr1, $i));
            break;
        }

        if ($arr1[$i] < $arr2[$j]) {
            $resArray[] = $arr1[$i];
            $i++;
        } else {
            $resArray[] = $arr2[$j];
            $j++;
        }
    }

    return $resArray;
}

$array = [3, 4, 1, 2, 5, 9, 6, 7, 8];
print_r(mergeSort($array));
```



*Дополнительно:*

- [Пузырьковая сортировка и все-все-все](https://habr.com/post/204600/)
- [Алгоритмы сортировки](http://algolist.manual.ru/sort/)
- https://m.habr.com/post/335920/
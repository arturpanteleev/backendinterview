# Symfony

## Compiler Pass

На этапе компиляции контейнер выполняет серию проходов для приведения своего содержимого в конечное состояние. Проходы являются реализацией интерфейса CompilerPassInterface и бывают 6 видов (в порядке исполнения): 

merge, beforeOptimization, optimization, beforeRemoving, removing, afterRemoving  — по умолчанию конфигурация компилятора уже содержит набор проходов.

- *before/after*— лишь «хуки» для пользовательских «проходов» и не используются стандартными (добавленными по умолчанию);
- *merge* — обрабатывает *расширения (extensions)* контейнера;
- *optimization* — оптимизируют контейнер, примерами могут служить: ResolveInterfaceInjectorsPass (преобразует [интерфейсные инъекции](http://martinfowler.com/articles/injection.html#InterfaceInjection)), CheckCircularReferencesPass (проверяет циклические ссылки в контейнере)
- *removing* — удаляют неиспользуемые сервисы, например, RemoveUnusedDefinitionsPass (удаляет неиспользуемые приватные сервисы)

В бандлах CompilerPass чаще всего используется для обработки тегов в контейнере

Пример из стандартного TwigBundle:

```php
    $definition = $container->getDefinition('twig');
    $calls = $definition->getMethodCalls();
    $definition->setMethodCalls(array());
    foreach ($container->findTaggedServiceIds('twig.extension') as $id => $attributes) {
        $definition->addMethodCall('addExtension', array(new Reference($id)));
    }
    $definition->setMethodCalls(array_merge($definition->getMethodCalls(), $calls));
```


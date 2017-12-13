# Git

## Work area / stage area / commit

![reset workflow](https://git-scm.com/book/en/v2/images/reset-workflow.png)

## reset(--sogt –hard –mixed) vs checkout

![reset checkout](https://git-scm.com/book/en/v2/images/reset-checkout.png)

|                                          | HEAD | Индекс | Рабочий Каталог | Сохранность РК? |
| ---------------------------------------- | ---- | ------ | --------------- | --------------- |
| **На уровне коммитов (без указания путей)** |      |        |                 |                 |
| `reset --soft [commit]`                  | REF  | NO     | NO              | YES             |
| `reset [commit]`                         | REF  | YES    | NO              | YES             |
| `reset --hard [commit]`                  | REF  | YES    | YES             | **NO**          |
| `checkout [commit]`                      | HEAD | YES    | YES             | YES             |
| **На уровне файлов (с указанием путей)** |      |        |                 |                 |
| `reset (commit) [file]`                  | NO   | YES    | NO              | YES             |
| `checkout (commit) [file]`               | NO   | YES    | YES             | **NO**          |

- Отличие merge от rebase
- rebase onto
- reflog
- cherry-pick
# git коротко

| Действие                                                                                                                                                                                                                                                   | Команды                                                                                                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Настройка                                                                                                                                                                                                                                                  | git config --global user.name "My Name"<br>git config --global user.name "s-ly"<br>git config --global user.email myEmail@example.com<br>git config --global user.email sergeylysov@outlook.com |
| Создание нового репозитория                                                                                                                                                                                                                                | $ mkdir Desktop/git_exercise/<br>$ cd Desktop/git_exercise/<br>$ git init                                                                                                                       |
| Определение состояния                                                                                                                                                                                                                                      | $ git status                                                                                                                                                                                    |
| Добавить отслеживаемые файлы                                                                                                                                                                                                                               | $ git add hello.txt<br>$ git add -A Все, что находится в директории.                                                                                                                            |
| Коммит (фиксация изменений)                                                                                                                                                                                                                                | $ git commit -m "Initial commit."                                                                                                                                                               |
| Подключение к удаленному репозиторию                                                                                                                                                                                                                       | $ git remote add origin https://github.com/tutorialzine/awesome-project.git                                                                                                                     |
| Отправка изменений на сервер                                                                                                                                                                                                                               | $ git push origin master <br>origin - имя удаленного репозитория<br>master - ветка, в которую необходимо внести изменения                                                                       |
| Клонирование репозитория                                                                                                                                                                                                                                   | $ git clone https://github.com/tutorialzine/awesome-project.git                                                                                                                                 |
| Запрос изменений с сервера                                                                                                                                                                                                                                 | $ git pull origin master<br>git pull origin main (новая версия имя ветки другое)                                                                                                                |
| Создание новой ветки                                                                                                                                                                                                                                       | $ git branch amazing_new_feature                                                                                                                                                                |
| Переключение между ветками                                                                                                                                                                                                                                 | $ git checkout amazing_new_feature                                                                                                                                                              |
| Слияние веток<br>test -> main<br>(В начале нужно перейти в ветку main)                                                                                                                                                                                     | $ git merge test                                                                                                                                                                                |
| Удалить ветку                                                                                                                                                                                                                                              | $ git branch -d awesome_new_feature                                                                                                                                                             |
| Отслеживание изменений в коммитах                                                                                                                                                                                                                          | $ git log                                                                                                                                                                                       |
| Отслеживание изменений в коммитах коротко                                                                                                                                                                                                                  | git log --oneline                                                                                                                                                                               |
| Возвращение файла к предыдущему состоянию                                                                                                                                                                                                                  | $ git checkout 09bd8cc1 hello.txt                                                                                                                                                               |
| Исправление коммита                                                                                                                                                                                                                                        | $ git revert b10cc123<br>$ git revert HEAD Самый последний коммит.<br>git revert HEAD --no-edit                                                                                                 |
| Настройка .gitignore                                                                                                                                                                                                                                       | *.log<br>build/<br>node_modules/<br>.idea/<br>my_notes.txt<br>Его надо коментить.                                                                                                               |
| Посмотреть, где находился.<br>Или посмотреть историю переходов.<br>Удобно смотреть какие есть комиты и их хеши.                                                                                                                                            | git reflog                                                                                                                                                                                      |
| история коммитов будет сброшена до указанного коммита                                                                                                                                                                                                      | git reset --hard a1e8fb5<br>git reset --hard (просто до последнего)                                                                                                                             |
| Сбросьте head до определенного коммита, не касаясь индексного файла и рабочего дерева. Все изменения, сделанные после этой фиксации, переносятся на этап “поставлены для коммита”. Далее вам просто нужно запустить git commit, чтобы добавить их обратно. | git reset ‐‐soft HEAD^                                                                                                                                                                          |
| Удаление локальной ветки                                                                                                                                                                                                                                   | git branch -d <local_branchname>                                                                                                                                                                |
| Удалить текущий пульт дистанционного управления                                                                                                                                                                                                            | git remote rm origin                                                                                                                                                                            |

Памятка из гита:

echo "# doc" >> README.md

git init

git add README.md

git commit -m "first commit"

git branch -M main

git remote add origin git@github.com:s-ly/doc.git

git push -u origin main

```
**git reset HEAD~**

Удаляет коммит. Откатывается на шаг назад.  Изменения в файлах остаются. Файлы красные.

**git reset --soft HEAD~**

Удаляет коммит. Откатывается на шаг назад.  Изменения в файлах остаются. Файлы зелёные.

**git reset --hard HEAD~**

Удаляет коммит. Откатывается на шаг назад. Изменения пропадают.

**git reset --hard HEAD**

Отменяет всё до текущего коммита. Изменения пропадают.
```

Вливает в текущую ветку изменения из *develop*. В начале нужно переключиться на ту ветку, в которую нужно влить изменения из *develop*. Например *git switch master* а потом *git merge develop*. данные из *develop* перетекут в *master*.

`git merge develop`

Красиво показывает ветки

`git log --graph --pretty=oneline --abbrev-commit --all --decorate`

Узнать размер репозитория

`git count-objects -vH`

# 

# Перейти на друой комит в гите

###### Смотрим комиты:

`git log --oneline`

###### Например такие коммиты:

cba021b (HEAD) перед доработкой деления остатка по новому

4ccf7ea дробная часть не верно думает

<mark>c5ec54c div normal</mark>

e516535 деление с остатком

915c109 div clean

1b810e1 div v1

d441a6d sub v1

c35a1b9 работает через раз

f790f07 shift ok

43a33eb shift work

b0cda2b make

f3a7ec6 new modeule div

###### Переходим на ветку <mark>div normal</mark> по хэшу:

`git checkout c5ec54c`

###### Вернуться к последнему коммиту в вашей текущей ветке <mark>(gentrifr)</mark>:

`git checkout gentrifr`

# 

# git LFS

Позволяет отделять большие файлы. В начале нужно установить утилиту, потом добавить файлы и пути, а потом как обычно.

`sudo apt install git-lfs  
(git lfs install в доках)  
git lfs version  
git init  
git lfs track "*.blend"  
git lfs track "Assets/Textures/*"`

# 

# Git. Краткое руководство по терминалу

[Git. Краткое руководство по терминалу | guides](https://netology-code.github.io/guides/git-terminal/git-terminal.html)

###### Выход из программы вывода текста:

нужно нажать клавишу q (сокращение от слова “quit” - покинуть) на английской раскладке клавиатуры.

# 

# link git

[Git - Book](https://git-scm.com/book/ru/v2)

[Введение в Git: от установки до основных команд](https://tproger.ru/translations/beginner-git-cheatsheet)

[Простое руководство по работе с git](https://rogerdudler.github.io/git-guide/index.ru.html)

[15. Удаление коммитов из ветки](https://githowto.com/ru/removing_commits_from_a_branch)

[Git. Коротко о главном](https://habr.com/ru/articles/588801/)

[Как использовать Git правильно](https://www.atlassian.com/ru/git)

# 

# git_doc

###### Посмотреть все существующие ветки в git:

Список всех локальных веток

`git branch`

Список всех удаленных веток

`git branch -r`

Список всех локальных и удаленных веток

`git branch -a`

Визуальное отображение графа предков для всех веток

`git show-branch`

создания пустого файла .gitignore

`touch .gitignore`


